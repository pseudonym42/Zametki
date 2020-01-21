# Collations
---
&NewLine;
&NewLine;

Databases have a collation and character set definitions. The charsets are the sets of characters that could be handled by the DB, the collation is the rule being used by DB for comparing and sorting characters.

Suppose that we have an alphabet with four letters: A, B, a, b. We give each letter a number: A = 0, B = 1, a = 2, b = 3. The letter A is a symbol, the number 0 is the encoding for A, and the combination of all four letters and their encodings is a character set.

Suppose that we want to compare two string values, A and B. The simplest way to do this is to look at the encodings: 0 for A and 1 for B. Because 0 is less than 1, we say A is less than B. What we’ve just done is apply a collation to our character set. The collation is a set of rules (only one rule in this case): “compare the encodings.” We call this simplest of all possible collations a binary collation.

So DB instances always have default character sets and collations which should be covering the most general cases, but if you know that your DB instance is going to be used for storing some non common characters, say spanish characters, then you might need to consider setting the spanish collation as default.

There are different types of collations used in DBs: connection collation, client collation, server collation, database collation etc. E.g. database collation displays the collation when characters get stored in the database, server collation displays how server engine is treating characters internally etc.

There are a number of various collation values, for example in MySQL DB: latin1_swedish_ci, utf8_general_ci, sjis_japanese_ci etc. The suffixes used in collations are:  \_ci for case insensitive, \_cs for case sensitive, \_bin for binary. For example in MySQL a latin1_swedish_ci collation is the default one. “Ascii only” collation is never used as it would cover only ascii characters and would work fine with only English. So most DBs use some “non-ascii” collation by default. The only problem is that there are a number of “non-ascii” collations i.e. latin1_swedish_ci collation is normally used with latin1 character set, but latin1 character set only covers part of non-ascii characters like ones in e.g. french and icelandic languages but does not cover e.g. chinese chars.

You need to make sure that collations for connection, client, server etc always match on your DB instance otherwise there might be errors when handling non-ascii characters if for e.g. your connection collation (this is how DB engine interprets the characters) is utf8_general_ci but collation for storing (this is how characters stored in the DB) data latin1_swedish_ci.
