---
layout: post
title: Intorduction of Wave File Format
tags: [Singal_Process] 
---

## Purpose

  When doing the project on speech recognition, the test audio data for voice processing adopts WAVE PCM file. 
    
  For quickly recapping the concept of WAVE PCM formay and avoiding to forget bit and pieces of this knowledge, I recorded this relevant information in this post.

## Abstract

  The WAVE file format is a subset of Microsoft's RIFF specfication for the storage of multimedia file. A RIFF file starts out with a file header followed by a sequence of data chunks. A WAVE file is often just a RIFF file with a single "WAVE" chunk whick consists of two sub-chunks, a "fmt" chunk specifying the data format and a "data" chunk containing the actual sample data. 

## The WAVE File Structure

The typically canonical WAVE format starts with the RIFF header:

<table style="width:100%" border-collapse: separate>
  <tr>
    <th>Field</th>
    <th>Bytes</th>
    <th>Endian</th>
    <th>Description</th>
  </tr>
  <tr>
    <th>Chunk ID</th>
    <th>4</th>
    <th>Big endian</th>
    <th>This chunk contains the letters "RIFF" in ASCII form.</th>
  </tr>
  <tr>
    <th>Chunk Size</th>
    <th>4</th>
    <th>Little endian</th>
    <th>This is the size of the rest of the chunk following this number - the size of the entire file in bytes minus 8 bytes, Chunk ID and Chunk Size.</th>
  </tr>
  <tr>
    <th>Format</th>
    <th>4</th>
    <th>Big endian</th>
    <th>This chunk contains the letters "WAVE".</th>
  </tr>
  <tr>
    <th>Sub-Chunk1 ID</th>
    <th>4</th>
    <th>Big endian</th>
    <th>This chunk contains letter "fmt".</th>
  </tr>
  <tr>
    <th>Sub-Chunk1 Size</th>
    <th>4</th>
    <th>Little endian</th>
    <th>16 for PCM.</th>
  </tr>
  <tr>
    <th>Audio Format</th>
    <th>2</th>
    <th>Little endian</th>
    <th>1 for PCM, 2 for ADPCM, 3 for IEEE floating point, 7 for u-law, and 67734 for WaveFormatExtensible.</th>
  </tr>
  <tr>
    <th>Num Channels</th>
    <th>2</th>
    <th>Little endian</th>
    <th>1 for Mono, 2 for stereo, etc.</th>
  </tr>
  <tr>
    <th>Sample Rate</th>
    <th>4</th>
    <th>Little endian</th>
    <th>8000, 16000, 44100, etc Hz. A typical value would be 44100, which is the same as an audio CD. The value of telephone and wireless microphone transmission, adequate for human speech, is 8000. The 16000 is used in most modern VoIP and VVoIP communication products.</th>
  </tr>
  <tr>
    <th>Byte Rate</th>
    <th>4</th>
    <th>Little endian</th>
    <th>This value is equals to Sample Rate * Num of Channel * Bytes per Sample.</th>
  </tr>
  <tr>
    <th>Block Align</th>
    <th>2</th>
    <th>Little endian</th>
    <th>This value is equals to Num of Channel * Bytes per Sample.</th>
  </tr>
  <tr>
    <th>Bits Per Sample</th>
    <th>2</th>
    <th>Little endian</th>
    <th>8 for 8 bits (1 bytes), 16 for 16 bits (2 bytes), etc.</th>
  </tr>
  <tr>
    <th>Sub-Chunk2 ID</th>
    <th>4</th>
    <th>Big endian</th>
    <th>This chunk contains the letters "data".</th>
  </tr>
  <tr>
    <th>Sub-Chunk2 Size</th>
    <th>4</th>
    <th>Little endian</th>
    <th>This value is equals to Num of Samples * Num of Channels * Bytes Per Sample</th>
  </tr>
  <tr>
    <th>Data</th>
    <th>*</th>
    <th>Little endian</th>
    <th>The actual sound data.</th>
  </tr>
</table>


#### Example: the structure of WAVE file format

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb">typedef struct WAV_FORMAT_T{
  // RIFF header
  char chunkID[4]; // Contains the letters "RIFF"
  int chunkSize; // This is the size of the entire file in bytes minus 8 bytes for the two fields not included in this count
  char format[4]; // Contains the letters "WAVE"

  // fmt sub-chunk
  char subchunk1[4]; // Contains the letters "fmt "
  int subchunk1Size; // 16 for PCM
  short audioFormat; // PCM = 1 
  short numChannels; // Mono = 1, Stereo = 2
  int sampleRate; // 8000, 16000, 44100, etc.
  int byteRate; //  sampleRate * numChannels * Bytes per sample
  short blockAlign; // numChannels * Bytes per sample
  short bitsPerSample; // The number of bytes for one sample including all channels

  // data sub-chunk
  char subChunk2[4]; // Contains the letters "data"
  int subChunk2Size; // Num of Samples * numChannels * Bytes per sample 
  char bytes[]; // The actual sound data (temporarily use 1 bytes)
} WAV_FORMAT;</span></code></pre></div>


## PCM Data Chunk

  The simplest and most common WAVE file is PCM. The PCM samples are just raw sample data and stored as integers. The bytes per sample field will indicate the range of the sample data:

 <table style="width:100%" border-collapse: separate>
  <tr>
    <th>Bytes per Sample</th>
    <th>Minimum Sample</th>
    <th>Maximum Sample</th>
  </tr>
  <tr>
    <th>1</th>
    <th>0</th>
    <th>255</th>
  </tr>
  <tr>
    <th>2</th>
    <th>-32768</th>
    <th>32767</th>
  </tr>
  <tr>
    <th>3</th>
    <th>-8388608</th>
    <th>8388607</th>
  </tr>
</table>

  In addition, the samples at a moment in time are called a sample frame. In a stereo file, a sample frame has 2 samples, one sample for the left channel and the other for right channel.

=========== To be continued.... ==========

## Reference
[1] [Wave File Format](http://wavefilegem.com/how_wave_files_work.html)

[2] [WAVE PCM soundfile format](http://soundfile.sapp.org/doc/WaveFormat/)

[3] [Parsing a WAV file in C](http://truelogic.org/wordpress/2015/09/04/parsing-a-wav-file-in-c/)

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.

Hope this post will help! :)
</p>
