# Design considerations for a resistive feedback divider in a DC/DC converter
电阻的大小选取对变换器效率（[[#^de0262|converter’s efficiency]]）, 输出电压准确度（[[#^47b047|output voltage accuracy]]）, 噪声敏感度（noise sensitivity）, 和稳定性（stability）都会存在影响

## converter’s efficiency ^de0262
对于==低功耗==的dc/dc变换器，反馈电阻的选择关乎效率问题。电阻反馈的通常设计分压电阻的总阻值非常大（up to 1M欧），减少反馈电阻分走电流。总阻值越小，需要的电流越大，而不计入输出，导致效率越低。
### example
![[Efficiency_with_different_feedback-divider_resistances.png]]

## output voltage accuracy ^47b047
采样电阻R越大，对于$I_{R1}$越小，而FB脚的要求电流是fixed的(a fixed feedback leakage current)，分给feedback引脚的$I_{R2}$越小.$$ V_{FB}=R_2 * (V_{out} - I_{FB} * R_1)/(R_1+R_2) $$如果$I_{R1}$越小，更少电流经过$I_{R2}$导致$V_{FB}$变小，$V_{FB}$/$V_{out}$的占比越小，而FB脚和内部做参考，导致抬升$V_{out}$
![[Leakage current going into the feedback pin  of a converter.png]]
