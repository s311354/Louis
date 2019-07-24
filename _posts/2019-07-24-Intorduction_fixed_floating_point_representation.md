---
layout: post
title: Introduction of fixed-point and floating-point representation
tags: [Math] 
---

## Purpose

   The fixed-point and floating-point notations are the basic representations on numerical analysis. In algorithm implementation, the notation frequently switches between fixed-point and floating-point. 

   For avoiding to forget bit and pieces of representation, I recorded a few pieces of information in this post.
   

## The representation of fixed-point numbers

   A flxed-point representation of number consists of three parts: the sign field (1 bit), integer field, and fractional field. 

   For the sign bit, on the computer, 0 is used to represent "+" and 1 is used to represent "-". (two's complement)

#### Example. The 8-bit (S2.5)

   $$ 1 \mid 00 \mid 10000 $$

   represents $$ (-0.1)_2 = -0.5 $$

## The representation of floating-point numbers

   A floating-point representation of number is by far more flexible. Any $$ x \neq 0 $$ may be written in the form

   $$ x = \pm (1.b_1b_2... ) \times 2^n$$ (binary version of scientific notation), 

   called the normalized representation of $$x$$. The normalized representation is achieved by choosing the exponent $$n$$ so that the binary point "floats" to the position after the first nonzero digit. 


#### Example. The  8-bit

   $$ (-1.101)_2 \times 2^2  = (-110.1)_2 = -6.5$$


=========== To be continued.... ==========

## Reference
[1] [Fixed-point and floating-point representations of numbers](http://www.math.drexel.edu/~tolya/300_float.pdf)

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.

Hope this post will help! :)
</p>
