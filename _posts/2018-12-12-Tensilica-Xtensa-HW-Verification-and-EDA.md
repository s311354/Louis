---                                                                                                                                                                                                                                      
layout: post
title: Tensilica Xtensa Hardward Verification and EDA
---

## Purpose

    Fortemedia inc. EDA tool utilizes Tensilica Xtensa HW verification and EDA tool to develop SOC design. The Tensilica was a company based in Sillicon Valley in the semiconductor intellectual property core business. It is now a par
t of Cadence Design Systems. The Xtensa processor architecture is a configurable, extensible, and synthesizable 32-bits RISC processor, emphasising on software single-clock.

    By using Tensilicas Xtensa dataplane processing units (DPUs), design teams can signifficantly reduce the development and verification time required by hand-coding RTL blocks in Verilog or VHDL. As these DPUs provide programabilit
y into the dataplane, changes can be made in firmware after sillicon production that extend the life of the product as standards develop and merket needs change.

    Two essential features of all Xtensa customizable processors:
<ul style="list-style-type:disc">
  <li> Configurability </li>
  <p> designers are offered a menu if checkbox and drop-down menu options so they can pick just the features they need <\p>
  <li> Extensibility</li>
  <p> designers can add their own instructions, registers, register lists, and much more using the Tensilica Instruction Extension (TIE) methodology - specifing the functional behavior of the new data path elements in the TIE languag
e (Verilog-like) and then the RTL and whole tool chain is automatically generated.<\p>
</ul>

## Xtensa Hardware Verification

### Verification Flows
    Tensilica provides three methods to verify HW design, ISS simulation, XTSC simulation and FPGA emulation. The both of ISS and XTSC simulations are system level verification. In addition, Tensilica verification scripts (e.g. soc_g
o) invoke CAD tool directly. For the diagnostic support, Tensilica provides a set diagnostic tests - Architectural Verification Programs (AVP) for all baisc-ISA opcodes, Micro-architecture Verification Programs (MVP) for checking int
erface, and Platform for runing programs and application in simulation (ISS simulation).



<h4><a name="TableContent"></a> Table of Contents</h4>
<h5><ol>
    <li><a href="#Arriving">Arriving and Living in the U.S</a></li>
</ol></h5>

<h3><a name="Arriving"></a> Arriving and Living in the U.S. </h3>

#### SEVIS and Social Secuity

<ul style="list-style-type:disc">
  <li> Driving while under the influence of alcohol is strictly against the law.</li>
  <li> The United States has an open container law, which means that you are not allowed to drink alcohol in public spaces, such as on the street or in a park.</li>
  <li> You are not allowed to camp or sleep in public areas.</li>
  <li> Hitchhiking is not allowed or advised.</li>
</ul>

## Reference

[1] [Tensilica Xtensa Hardware Verification and EDA](https://www.cadence.com/content/cadence-www/global/en_US/home/training/all-courses/86065.html) 
[2] [Xtensa Processors Wiki](https://www.semiwiki.com/forum/showwiki.php?title=Tensilica:Xtensa-Processors-Wiki)
[3]

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.
Hope this post will help! :)
</p>
