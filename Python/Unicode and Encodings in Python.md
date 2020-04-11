# How Python handles encodings?

---

There are two main concepts of encodings in Python: file encoding and so called strings/text encoding.

Before we look into what these encodings mean let us have some definitions and understand why encoding is required.

NOTE: the file encoding corresponds to `sys.getfilesystemencoding()` and strings encoding can be obtained via - `sys.getdefaultencoding()`

A code point is the atomic unit of information. Any text is a sequence of code points. Each code point is a number which is given meaning by the Unicode standard. For example character encoding scheme ascii uses 128 code points, which means when text is parsed and it is meant to be encoded with ascii then each character gets a code point in the range of 0 to 127.

A code unit is the measure (which consists of bits) which is used by an encoding. In ascii it is 7-bits, in UTF-8 this means 8-bits, in UTF-16 this means 16-bits, in UTF-32 this means 32-bits. A single code unit may represent a full code point, or part of a code point. For example, consider a string of the letters "abc" followed by 𐐀, which has Unicode code point number 10400 i.e. U+10400. So this string contains:
* four characters
* four code points
* and either:
    * four code units in UTF-32 (00000061, 00000062, 00000063, 00010400)
    * five code units in UTF-16 (0061, 0062, 0063, d801, dc00)
    * or seven code units in UTF-8 (61, 62, 63, f0, 90, 90, 80)

Python 2 default file encoding is UTF-8 and strings encoding is ascii (note that Python 3 uses UTF-8 for both). This is what you'd get in Python 2 console:

    >>> import sys
    >>> sys.getdefaultencoding()
    'ascii'
    >>> sys.getfilesystemencoding()
    'UTF-8'

So if you try just print the above string "abc𐐀”, you will get the below - as python will encode it with UTF-8 on the fly and print it out:

	'abc\xf0\x90\x90\x80'

As you can see it corresponds to 7 code units as explained above, because UTF-8 was used. But if you want to see how it would get encode say using UTF-16 you could do this:

	>>> "abc𐐀".decode("utf-8").encode("utf-16")
    '\xff\xfea\x00b\x00c\x00\x01\xd8\x00\xdc'

As you can see it corresponds to 5 code units as explained above. Ignore the first `\xff\xfe` which stands for BOM (byte order mark and is U+FEFF)

So why do we need all this encodings? We can't write code points to disk, or send them through the network. We still need actual ones and zeroes, if we want to work with a computer. The way to do that, is through the use of encodings. Encoding and decoding actually happens when code points get converted into code units. The process of turning symbols defined in the Unicode into bits is called encoding, the process of reading bits (1s and 0s), making sense out of them, and getting back symbols or characters is called decoding.

## File encoding:
This is the encoding used by Python parser to encode the source file (.py files). To specify this encoding use the following string as the 1st or 2nd comment on the source file:

	# -*- coding: <encoding name> -*-

For more details refer to https://www.python.org/dev/peps/pep-0263

This encoding is basically used when the file Python parser goes through the source file and turns that into bytes. If the encoding is not set, then the default encoding is ascii in Python 2 and UTF-8 in Python 3. So if you want to use non-ascii chars in your Python 2 source file, e.g. adding comments or some strings in say Russian language then you must add the above line otherwise you will get an error

## Strings encoding:
Unicode was designed to handle non-english characters which are outside ASCII. For e.g. symbol £ has unicode U+00A3 (hex).
As was mentioned earlier - default encoding in Python-2 is ASCII (maximum number of characters is 128, excluding extended ASCII) and default encoding in Python-3 is UTF-8 (maximum number of characters is 256).

There are always two types of string data types in Python (regardless of the Python version):

#### Unicode-like data type:
A unicode type of string. Abstract symbols with no bit representation. We'll need to encode them if we want to get the final binary data. unicode.encode() -> bytes. This data type is unicode in Python 2 and str in Python 3.

#### Byte-like data type:
A binary type of string. These are just ones and zeroes. We'll need to decode them if we want to get the symbols back. bytes.decode() -> unicode. This data type is str in Python 2 and bytes in Python 3.

---

Let’s see how Python 2 handles strings encoding. For example, in Python-2 terminal if you write 'äèß' then you will get:

    '\xc3\xa4\xc3\xa8\xc3\x9f’

if terminal default file encoding is ‘utf-8’. You can also check your terminal default encoding via:

    sys.stdout.encoding or sys.getdefaultencoding()

The reason for this is - terminal works with bytes, so what terminal does on the fly is this:

    u'äèß’'.encode('utf-8')

which results in:

    '\xc3\xa4\xc3\xa8\xc3\x9f’

But when you writing a script (outside terminal) before you can save text to disk you need to encode it using UTF-8/16/32(or other encoding) just like terminal doing it on the fly. You can encode it with

    uni_str.encode('UTF-8')

for example:

    u'äèß’'.encode('utf-8')

which is exactly the same thing terminal produces

    '\xc3\xa4\xc3\xa8\xc3\x9f’

To decode it:

    utf_str.decode('UTF-8')

Python-2 needs `<type ‘str’>` to send data to external system or even save data to disc, so - you cannot save an object `<type 'unicode'>` to disk or even send it to other external system as is, you need to convert it to `<type 'str'>` via encoding.

The problem with different encodings is that they are not quite accurate, for e.g. you have string "Ivan Krstić". If you use UTF-8 encoding which takes 2 bytes for character "ć" as this character cannot be encoded with ASCII then after encoding the string will be like 'Ivan Krsti\xc4\x87' (so the after encoding the string actually is a string of bytes i.e. so called byte-string) and if you check the length of the string you will get 12 not 11. For UTF-16 encoding number also will be different etc.
Also note that when you get strings from some external system it is normally encoded so in Python-2 you would need to decode it as explained above.
Also note that default encoding is used by default for both encoding and decoding. For example default encoding Python-2 is ASCII, so if you type this in Python-2 terminal:

	>>> "äèß " + u"abc"

then implicitly Python-2 terminal will try to decode 'äèß' using default encoding (ASCII) so two texts could be concatenated (so it tries to make them both unicode before concatenating) and it will give you error - `UnicodeDecodeError`.

Note: when you create a string in Python-2 it by default creates a bytestring (`<type 'str'>`), so

    text = "random_text"

is going to be a `<type 'str'>`. If it is required for all string by default get created as unicode use this import:

	from __future__ import unicode_literals

at the beginning of the script. But this still does not let you create a non-ascii char containing string in the script itself, for that you need to add the following line as the first line of the script:

	# -*- coding: utf-8 -*-

Also note that `<type 'unicode'>` and `<type 'str'>` are child types of `<type 'basestring'>`

See below links for more information:

http://lucumr.pocoo.org/2014/1/5/unicode-in-2-and-3/

http://farmdev.com/talks/unicode/
