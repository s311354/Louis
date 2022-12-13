---
title: Introduction of Hamming Code
date: 2022-12-12
categories:
- louissrliu
- features
tags:
- mathematics
- telecommunication
toc: true
mathjax: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/alviso.jpeg
---

Either computer data storage or telecommunication, regardless of the data storages and transmission, is non-zero probabilities that the data could be changed while it's being stored or transmitted. There is always a code-word with block length without free bit-errors. That means the data probably could be changed while it is being processed or transmitted. If the machine can't locate the position of the error and correct it, the information might be lost forever.

<!-- more -->

## Introduction ##

Early, [Richard Hamming][richard], the mathematician and computer scientist, grew increasingly fustrated with having to restart his programs from scratch due to detected errors in the relays. After that and over the few years, he worked on the problem of error-correction, developing an increasingly powerful array of algorithms, which is now known as Hamming code. This invented code still remains in use today in application such as Error correction code memory.

In this post, I would like to try to walk through how Hamming code were invented mathematically, and also the fundamental theory of error detection and error correction slightly.

## Error Detection & Error Correction ##

Error detection and error correction are different techniques that enable reliable delivery of digital data over unreliable communications channels. Usually error detection is much simpler whereas error correction could be more complicated. In addition, error detection and error correction does not always work. That means all error-detection and error correction methods only work below a certain error rate. Above that rate, the line is simply not usable.

### Error Detecting Code ###

#### Parity ####

Parity bit appended to a binary number provides the simplest form of error detecting code. If a single bit in the resulting value is changed, then it will no longer have the correct parity: changing a bit in the orginal number gives it a different parity than the recorded one, and changing the parity bit while not changing the number it was derived from again produces an incorrect result.

The following is a simple example of attaching 1 parity bit to 7 bits of data, making the bit string to always have even number of 1s. Therefore, the XOR of the 8-bit data is always 0.

|  Data (7 Bits)      |    Count of 1-Bits       |  Parity Bit   |  Data Including Parity (8 Bits)   |  XOR   |
| ------------- |-------------  | ------- | ------- | ------- |
|    0000000    |    0          | 0       | 00000000       | 0       |
|    1010001    |    3          | 1       | 10100011       | 0       |
|    1101001    |    4          | 0       | 11010010       | 0       |
|    1111111    |    7          | 1       | 11111111       | 0       |

If the bit string has one single error, either in the data or at the parity bit position, the number of 1st in the bit string will not be even, and XOR will not be 0. However, if there are even number of errors in the bit string, the error could never be detected, as XOR would remain 0.

Moreover, parity does not indicate which bit contained the error, even when it can detect it. The data must be discarded entirely and re-transmitted from scratch, which could be inconvenient and time-consuming.

### Error Correcting Code ###

#### Repetitions ####

The repetition code is one of the most basic error-correcting codes. In order to transmit a message over a noisy channel that may corrupt the transmission in a few places, the idea of the repetition code is to just repeat the message several times in order to ensure that it was sent correctly.

The following is a simple example of repeating every bit from 3 bits of data 3 times.

|  Data (3 Bits)      |    Number of Repetitions       |  Data Including Repetitions   	|
| ------------- |-------------  | ------- |
|    000    |    3          | 000000000       |
|    010    |    3          | 000111000       |
|    101    |    3          | 111000111       |
|    111    |    3          | 111111111       |

If the data including repetitions received are not identical, an error occurred during transmission. If the channel is clean enough, most of the time only one bit will change in each triple. Therefore, 001, 010, and 100 each correspond to 0 bit, while 110, 101, and 001 correspond to a 1 bit, with the greater quantity of digits that are the same ('0' or a '1') indicating what the data bit should be.

However, such repetition code cannot correctly repair all errors, but could be mitigated by using larger number of repetitions. The more bit repetitions to vote, the more robust the error correction code to error rate. The drawback is that it will reduce throughput and the efficiency drops drastically as we increase the number of times each bit is duplicated.

## Hamming Codes ##

Hamming codes can detect one-bit and two-bit errors, or correct one-bit errors without detection of uncorrested errors. In mathematical terms, Hamming codes are a class of binary linear code. For each integer $r \geq 2$, where $ r $ is the parity bits, there is a code-word with block length $ n = 2^r - 1 $ and message length $ k = 2^r - r - 1$. Hence the rate of Hamming codes is $ R = k/n = 1 - r / (2^r - 1)$, which is the highest possible for codes with minimum distance of three and block length $ 2^r - 1$. When $ r $ is very large enough, almost all the bits in the Hamming code are transmitted.

Here is an example, for $ r = 3 $, the Hamming code has $ n = 7 $ and $ k = 4 $. In the 7 bits of the Hamming code, 4 bits are the message we wanted to transmit, the rest 3 bits are parity bits which protects the message. In 1950, Hamming introduced this [7, 4] Hamming code, it encodes four data bits into seven bits by adding three parity bits.

 consists of 4 data bits, $d_1, d_2, d_3, d_4$, and 3 parity bits, $p_1, p_2, p_3$. As is shown in the following graphical depiction, the parity bit $p_1$ applies to data bits $ d_1, d_2$ and $d_4$, the parity bit $p_2$ applies to the data bits $d_1, d_3$, and $d_4$, the parity bit $p_3$ applies to the data bits $d_2, d_3$, and $d_4$. When there is no error in the bits, none of the parities will break.

<p align="center">
{% img center https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/Hamming.png %}
The Binary Hamming(7,4) code (with r = 3)
</p>

### Binary Linear Algebra ###

The encoding and decoding of Hamming codes could be represented as linear block codes. The property of linearity is the sum of any two codewords is also a code word, and they are applied to the soruce bits in blocks. Let's try to be the genius Richard Hamming in 1950s and come up with a generic Hamming code for different numbers of error-correction bits.

The number of parity bits $r$, and each parity bit is used only once in one parity. So there are $r$ parities, and the number of different parity states that Hamming code could represent is $ 2^r $. One parity state has to represent the state that the code has no error. Each of the rest parity states has to represent the state that one unique bit has an error. The number of the rest parity states is $ 2^r - 1 $, therefore, $r$ parity bits could be used for error-correcting codes up to $2^r - 1$ bits. That's why Hamming code has $r$ number of error-correction bits, the block length $n = 2^r - 1$ and message length $k = 2^r - r - 1$.


#### Construction of Generator & Parity-check Matrix ####

Hamming code could be represented using (binary) linear algebra. Consider Hamming(7,4) code, the data bits could be represented using a column vector $d\in R^4$, where

$$
d = \begin{bmatrix}
        d_{1}  \\
        d_{2}  \\
        d_{3}  \\
        d_{4}  \\
    \end{bmatrix}
$$

There are two matrics: generator matrix **G** and parity-check matrix **H** for linear block codes. The construction of **G** and **H** is in standard (or systematic) form:

The generator matrix of a linear (n, k) code:

$$ G \doteqdot (I_k | -A^T) $$

The parity-check matrix of a linear (n, k) code:

$$ H \doteqdot (A | I_{n-k}) $$

Regardless of form, **G** and **H** also must satisfy:

$ HG^T = 0 $, an all-zeros matrix.

Encoding:

To encode the data bits **d**, the codeword **x** is given by the standard matrix product $x = G^Td$, where the generator matrix **G** from is

$$
G^T = \begin{bmatrix}
        1    & 1    & 0    & 1 \\
        1    & 0    & 1    & 1 \\
        1    & 0    & 0    & 0 \\
        0    & 1    & 1    & 1 \\
        0    & 1    & 0    & 0 \\
        0    & 0    & 1    & 0 \\
        0    & 0    & 0    & 1 \\
    \end{bmatrix}
$$

Note: the generator matrix **G** could be easily derived from the bit sequence and parity table.

The Hamming(7,4) encoded bits **x** would be

$$
x = G^Td
  = \begin{bmatrix}
        1    & 1    & 0    & 1 \\
        1    & 0    & 1    & 1 \\
        1    & 0    & 0    & 0 \\
        0    & 1    & 1    & 1 \\
        0    & 1    & 0    & 0 \\
        0    & 0    & 1    & 0 \\
        0    & 0    & 0    & 1 \\
    \end{bmatrix}
    \begin{bmatrix}
        d_{1}  \\
        d_{2}  \\
        d_{3}  \\
        d_{4}  \\
    \end{bmatrix}
  = \begin{bmatrix}
        (d_{1} + d_{2} + d_{4}) mod 2  \\
        (d_{2} + d_{3} + d_{4}) mod 2  \\
        d_{1}  \\
        (d_{2} + d_{3} + d_{4}) mod 2  \\
        d_{2}  \\
        d_{3}  \\
        d_{4}  \\
    \end{bmatrix}
  = \begin{bmatrix}
    p_{1} \\
    p_{2} \\
    d_{1} \\
    p_{3} \\
    d_{2} \\
    d_{3} \\
    d_{4}
    \end{bmatrix}
$$

Error Detection:

Suppose that the reveived data is **x'**, and **x'** may or may not equal to **x**, where

$$
H = \begin{bmatrix}
        1    & 0    & 1    & 0   & 1   &0  & 1 \\
        0    & 1    & 1    & 0   & 0   &1  & 1 \\
        0    & 0    & 0    & 1   & 1   &1  & 1 \\
    \end{bmatrix}
$$

The property of **H** is that each column in **H** is actually binary index sequence, 1, 2, 3, etc. This property will be very useful for error correction.

Error Correction:

Suppose that the actual Hamming encoded code reveived would be

$$ x' = x + e_{i} $$

Thus, parity checking would just be

$$
z = Hx'
  = H(x + e_{i})
  = Hx + He_{i}
  = 0 + He_{i}
  = [1 \: 2 \: 3 \: 4 \: 5 \: 6 \: 7]_{10}e_{i}
  = (i)_2
$$

The resulting value would be non-zero if there is a bit error. In order to fix the bit error, just simply flip the bit value at address **x**.

Now, as you can see, the Hamming code could be derived from the bit sequence and parity table. The way Richard Hamming worked for error correction must be some mathematical motivations behind. However, I did not get time to reserach and explore. Thay's why Richard Hamming was a famous mathematician but I am not. :)

## Reference ##

- [Wiki: Hamming code](https://en.wikipedia.org/wiki/Hamming_code)

- [Wiki: Hamming distance](https://en.wikipedia.org/wiki/Hamming_distance)

- [Wiki: Error detection and correction](https://en.wikipedia.org/wiki/Error_detection_and_correction)

- [Wiki: List of mathematical symbols by subject](https://en.wikipedia.org/wiki/List_of_mathematical_symbols_by_subject)

[richard]:https://en.wikipedia.org/wiki/Richard_Hamming "https://en.wikipedia.org/wiki/Richard_Hamming"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
