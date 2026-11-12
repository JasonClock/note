# Design considerations for a resistive feedback divider in a DC/DC converter
电阻的大小选取对变换器效率（[[#^de0262|converter’s efficiency]]）, 输出电压准确度（[[#^47b047|output voltage accuracy]]）, 噪声敏感度（[[#^f22750|noise sensitivity]]）, 和稳定性（[[#^dc54ff|stability]]）都会存在影响

## converter’s efficiency ^de0262
对于==低功耗==的dc/dc变换器，反馈电阻的选择关乎效率问题。电阻反馈的通常设计分压电阻的总阻值非常大（up to 1M欧），减少反馈电阻分走电流。总阻值越小，需要的电流越大，而不计入输出，导致效率越低。
### example
![[Efficiency_with_different_feedback-divider_resistances.png]]

## output voltage accuracy ^47b047
采样电阻R越大，对于$I_{R1}$越小，而FB脚的要求电流是fixed的(a fixed feedback leakage current)，分给feedback引脚的$I_{R2}$越小.$$ V_{FB}=R_2 * (V_{out} - I_{FB} * R_1)/(R_1+R_2) $$如果$I_{R1}$越小，更少电流经过$I_{R2}$导致$V_{FB}$变小，$V_{FB}$/$V_{out}$的占比越小，而FB脚和内部做参考，导致抬升$V_{out}$(在实际情况下其实$I_{FB}$不是特别固定的，会有一点移动，以指定的最大值作为最坏情况)
![[Leakage current going into the feedback pin  of a converter.png]]
### example
![[VFB and VOUT as functions of  feedback-divider resistance.png]]

## noise sensitivity ^f22750
The resistive divider is ==one source of noise== for a converter.
反馈电路的噪声来源有：
- 热噪声thermal noise (=$4*K_B*T*R$,$K_B$为玻尔兹曼常数Boltzmann’s constant) 分压电阻越大，会增加噪声
- 开关噪声
- 无线电波和手机信号等
电阻越大，会把更多的噪声耦合(Couple)进入变换器。eg.无线电波 手机信号等

噪声与FB引脚相连，导致噪声进入闭环回路增益（closed-loop gain）并反应到输出output上。
通过更小的反馈电阻、良好的布局和屏蔽（shielding）降低susceptibility（易感性）

## Control loop, transient response, and converter stability ^dc54ff
稳定的变换器的相位裕度至少45度，减少/消除输出电压振铃，保护$V_{in}$或者负载跳变时电压敏感元器件不受损坏
通过引入前馈电容$C_{FF}$来引入zero和pole点频率来增加相位裕度和交叉频率，获得更高的带宽和稳定性
根据传递函数可以得到
$f_Z$=1/$2\pi R_1C_{ff}$
$f_p$=1/$2\pi R_1//R_2$
![[Buck converter’s load-transient response with different  R1, R2, and CFF value.png]]