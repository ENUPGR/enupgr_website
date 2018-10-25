---
layout: post
author: Peter Aaby
title: HMGCC Pen cipher challenge 
description: "One of the challenges from UK Cyber Challenge F2F hosted by HMGCC"
tags: [HMGCC,UK Cyber Security Challenge]
image:
# background: triangular.png
---
On Friday 11 August, [HMGCC](https://www.hmgcc.gov.uk/) hosted the third out of five Face to Face events at the UK Defence Academy which is part of the 2017 [Cyber Security Challenge UK](https://www.cybersecuritychallenge.org.uk/competitions/face-to-face) masterclass qualifiers.

As host, HMGCC found the perfect environment to host a cyber security challenge and it was within this environment that they managed to hide one of their challenges in plain sight; a swag pen with HEX characters forming the letters **HMGCC**. It took the better of an hour before officials had to announce that everybody should "search their tables for any foreign objects". A pen had for each team member been placed on our desks overnight, and I personally (as well as everyone else) did not think any more of this until the hint was announced. Below is a photo of the pen.

![center-aligned-image](images/posts/hmgcc-hex-pen.jpg){: .align-center}

# Pen HEX values digitalised
The obvious first step was to digitalise the HEX values found on the pen. However, some of the letters and digits caused issues as it was hard to distinguish S and 5 from each other. Therefore, first attempt failed and my team mate Eed gave a helping hand by checking for typo's. Below is the final digital printout which is the basis of the challenge.

```
A5     97  0E      CD  3CF2A0   F3BFAF  2418FC
E2     CB  C906  6EBD  3A       F2      CA
B972E5CB9  72  EA  59  70  ECD  3C      F2
A0     F3  BF  AF  24  18  FC   E2      CB
C9     06  6E      BD  3AF2CA   B972E5  CB972E
```
With the HEX values captured, an initial conversion from HEX to ASCII was attempted using [asecuritysite's ASCII, Hex, Base-64 and Binary convertor](https://www.asecuritysite.com/coding/ascii?hex=62657274&hex=62657274). The results did not return 100% positive. However, the ASCII conversion gave away that the HEX values had been duplicated. At this moment, we got stuck and the time presure with other challenges due made us decide on utilising one of our free hints (hint disclosed in next section).

We did not have enough time to program anything during the challenge, thus the above website was used for analysis and conversion from HEX to ASCII. After the challenge, I decided that it would be interesting to script a solution and below code snippet shows how it could have been done using Python 2.7. 

```python
import binascii

hex = """   A5     97  0E      CD  3CF2A0   F3BFAF  2418FC
            E2     CB  C906  6EBD  3A       F2      CA
            B972E5CB9  72  EA  59  70  ECD  3C      F2
            A0     F3  BF  AF  24  18  FC   E2      CB
            C9     06  6E      BD  3AF2CA   B972E5  CB972E"""

hex = hex.translate(None, ' \n') #remove whitespace and newlines
print "Hex in ASCII\t",binascii.unhexlify(hex)
print "Count of bytes:",len(hex)/2, "bytes\n"
```

Python printout:

```
Hex in ASCII	���<��$����n�:�ʹr�˗.���<��$����n�:�ʹr�˗.
Count of bytes: 56 bytes
```

As mentioned before, the data duplication can be seen above by the repeating pattern of __�ʹr�˗.__ in the middle and at the end of the HEX string. Effectively, the HEX stream can be cut in half or in its entirety. I leave it thus expect the result to come out twice when solved. Double the points, right? :)

Being under time pressure, we decided to leave two team mates working on other challenges, while me and Eed continued with help from the new hint.


# Hint (spoilers and solution below)
As time progressed, we decided on utilising one of our free hints. It turned out, that we had already passed the first two hints prepared by the officials and we could therefore only get the last one. Mixed feelings, as it meant we had gotten really close, but if we couldn't make use of the hint then time would have been wasted. 

You are waiting for the hint, aren't you? 

Ok, so the hint is simply __SMS__

We did not know what to make of it, but raced to Google and  started searching for SMS decoders and quickly discovered an [Online SMS PDU Decoder](https://www.diafaan.com/sms-tutorials/gsm-modem-tutorial/online-sms-pdu-decoder/). This site takes HEX values as input and convert data-streams into readable SMS text messages. Win?! Sadly not, a dead end and we returned to research further into SMS protocols.

At this moment, we still didn't really know if we had decoded the HEX values correctly, but the duplicated values pointed in the right direction. Thankfully, it didn't take long till we discovered [GSM 03.38](https://en.wikipedia.org/wiki/GSM_03.38), a 7-bit GSM alphabet which could have been used to generate the hidden message. 

From the online conversion on [asecuritysite](https://www.asecuritysite.com/coding/ascii?hex=62657274&hex=62657274), each HEX value had already been converted to binary and we simply copied this manually into a text editor and began deciphering by cutting the bit stream into chunks of 7. 

Below is a snippet of the manual process which was used to positively produce the correct answer. HURRAY!

```
Binary	HEX	ASCII
1010010 52	R
1100101	65	e
1100001 61 	a 
1101100 6C 	l
.
.
.
.
1110100 74 	t 
1110101 75 	u 
1110010 72 	r 
1100101 65 	e 
```

Fully automated using Python 2.7 and the bitstring module.

```python
import bitstring
binaryString = bin(int(hex,16))
bytes = []
b = bitstring.BitArray(binaryString)
for byte in b.cut(7): #cut bytestream into chunks of 7-bytes
    print chr(int(byte.bin,2)) #conv datatypes and print bin>int>char
```

Python printout:

```
R
e
a
l
i
s
e
 
y
o
u
r
 
c
y
b
e
r
 
f
u
t
u
r
e
.
.
.
.
.
.
.
R
e
a
l
i
s
e
 
y
o
u
r
 
c
y
b
e
r
 
f
u
t
u
r
e
.
.
.
.
.
.
.
```

Browsing to [HMGCC's](http://www.hmgcc.gov.uk) website, have a look at the top left logo and picture with a similar quote. Picture linked below.

Thanks to HMGCC for the challenge, now to the second pen that was handed out by the end of the Face to Face challenge :)

<figure class="half">
	<img src="http://www.hmgcc.gov.uk/assets/img/logo.png" alt="">
</figure>
