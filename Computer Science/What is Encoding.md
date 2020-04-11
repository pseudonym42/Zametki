# What is encoding?
---

People do not understand binary data, they understand pictures, symbols and letters.

Computers can only understand binary data and it stores everything as binary data - just 1s and 0s.

So we could reliably store, compress/decompress and transmit our texts, music, video and other data we have to encode
all that data so it can be stored by computers.

There are loads of different coding formats for music (e.g. mp3), video (e.g. mp4, H.264), pictures (e.g. jpeg), texts (e.g. UTF-8, ASCII) etc

Whenever any binary data is passed over to any consumer, additional meta data must be passed with it as well, so the
consumer knows how to "read" (decode) that data. This applies to text data as well.

Process of converting data into binary is called encoding, the reverse process is called decoding.

It is worth to mention that there's a widely used Base64 encoding. Base-64 encoding is a way of taking binary data and turning it into text so that it's more easily transmitted in things like e-mail and HTML form data. When you have some binary data that you want to ship across a network, you generally don't do it by just streaming the bits and bytes over the wire in a raw format. Why? because some media are made for streaming text. You never know -- some protocols may interpret your binary data as control characters (like a modem), or your binary data could be screwed up because the underlying protocol might think that you've entered a special character combination (like how FTP translates line endings). So to get around this, people encode the binary data into characters.

In web domain common usage of the Base64 encoding is to store small images in the HTML or CSS source code (see https://css-tricks.com/data-uris/) for performance reasons
as you don't need to make additional HTTP requests for each image or icon.

From here onward we are going to talk about text encoding/decodings.

Before we start talking about text encoding we need to understand what Unicode.

## What is Unicode?

Unicode is a just set of characters. Each character has a corresponding code known as codepoint.
This codepoint has format `U+xxxx`. Using these codepoints we can represent all characters in almost any language in this world.

This character set is used by one of the most widely used text encodings in the world - UTF-8, UTF-16 and UTF-32.

Here the number indicates - the number of bits it uses to represent a codepoint.

UTF-8 is dominant on the web and can be used to encoding all possible Unicode codepoints. Since HTML5, it has been the recommended encoding.
See https://html.spec.whatwg.org/multipage/semantics.html#charset

But both .NET and Java environments are founded on a UTF-16 character type. Here are the most important differences between those two:

UTF-8:
* its code unit is 8-bits
* uses 1 to 4 code units per codepoint. For example: 00100100 for "$", 11000010 10100010 for "¢", 11100010 10000010 10101100 for "€"
UTF-16
* its code unit is 16-bits
* uses 1 to 2 code units per codepoint. For example: 00000000 00100100 for "$", 11011000 01010010 11011111 01100010 for "𤭢"

Note that codepoints are represented with hexadecimal numbers, as hexadecimal numbers are easier to read and work with for humans.

It is important to understand the following to avoid confusion:
UTF-8 uses the 2 high bits (bit 6 and bit 7) to indicate if there are any more bytes: only the low 6 bits are used for the actual character data. That means that any character over 7F (127 in decimal) requires (at least) 2 bytes. For example, the letter á Unicode codepoint is U+00E1 (E1), or 225 in decimal. 225 in binary is 11100001, but
we have to use 2 bytes to encode it in UTF-8 (because only the first 128 characters use only one byte). So we can encode the letter á in UTF-8 like this:

11000011 10100001 (C3 A1 in hexadecimal)

As was mentioned earlier, whenever a piece of text is passed over the network, a text gets encoded using some encoding. This information
must be passed to the receiver so it can be decoded. This way, when receiver gets to open a text file that contains the bytes C3 A1 and the program
interprets the encoding as UTF-8, you will see an á. Otherwise if the program thinks the encoding is e.g. Latin-1 or something like that, the user
will instead see whatever C3 A1 mean in that encoding


## How is Python 3 handling text encoding?
Python 3 default encoding is UTF-8. It has concept of string and bytes (which is basically just an array of bytes).
If you use Python it can be easily seen how strings are handled:

```Python
>>> 'ап'.encode()
b'\xd0\xb0\xd0\xbf'
>>> 'ап'.encode().decode()
'ап'
>>>
```
It can be seen from the above that 'ап' is represented as D0B0 D0BF in hexadecimal.


## What is URL encoding?
URLs can only be sent over the Internet using the ASCII character-set. If a URL contains characters outside the ASCII set, the URL has to be converted.
URL encoding converts non-ASCII characters into a format that can be transmitted over the Internet: it replaces non-ASCII characters with a "%" followed
by hexadecimal digits. URLs have a number of reserved characters which must be converted. For example, URLs cannot contain spaces, so URL encoding normally
replaces a space with a plus (+) sign, or %20. 
