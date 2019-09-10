---
layout: post
title: Introduction of feature representations of the speech signal
tags: [Singal_Process] 
---

## Purpose
When doing the project on speech signal processing, we need to know the different feature representations of the speech signal. Any decisions in pattern recongnition system would affect classification algorithm. 

For quickly recapping the conception of features of speech signal and avoiding to forget bit and pieces of this knowledge, I recorded this relevant information in this post.

## Abstract
Through more than 30 years of speech reconginizer research, many different feature representations fo the speech signal have been suggested and tried. The most popular feature representation currently used is the Mel-frequency Cepstral Coefficients (MFCC). Another popular speech feature representation is known as Relative Spectral Transform - Perceptual Linear Prediction (RASTA-PLP).

#### MFCC
MFC is a representation of the short-term power spectrum of a sound, based on a linear consine transform of a log power spectrum on a nonlinear mel scale of frequency. The coefficients are derived from a type of cepstral representation of the audio clip.

MFCCs are commonly derived as follows:
<h6><ol>
    <li>Take the Fourier transform of a signal.</li>
    <li>Map the powers of the spectrum obtained above onto the mel scale.</li>
    <li>Take the logs of the powers at each of the mel frequencies.</li>
    <li>Take the discrete cosine transform of the list of mel log powers.</li>
    <li>The MFCCs are the amplitudes of the resulting spectrum.</li>
</ol></h6>

#### RASTA-PLP
The RASTA-PLP uses bandpass filtering in the log spectral domain then RASTA filtering removes slow channel variations. To put it simply, it is a separate technique that applies a bandpass filter to the energy in each frequency subband in order to smooth over short-term noise variations and to remove any constant offset resulting from static spectral coloration in the speech channel, e.g. from a telephone lone.


=========== To be continued.... ==========

## Reference
[1] [PLP and RASTA (and MFCC, and inversion) in Matlab](https://labrosa.ee.columbia.edu/matlab/rastamat/)

[2] [Mel-frequency cepstrum](https://en.wikipedia.org/wiki/Mel-frequency_cepstrum)

[3] [Rasta filtering](https://en.wikipedia.org/wiki/Rasta_filtering)

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.

Hope this post will help! :)
</p>
