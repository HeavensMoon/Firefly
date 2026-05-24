---
title: XM Power Kit
published: 2026-05-17
description: 关于数控电源，万用表，示波器，信号发生器的四合一复刻记录
image: ./cover.jpg
tags: [电路设计]
category: 电路设计
draft: false
---
# 数控电源部分
## 输出泄放电路
###  让输出电压快速掉下去
数控电源输出通常需并联大电容（比如输出24V调成5V），输出电容还存着电，电压会慢慢下降
$$
E=\frac{1}{2}CU^2
$$
So输出泄放电路可以在降压或关闭输出自动接一个耗电通道，把电容里的电快速放掉   
### 防止回灌
比如接MCU，MOS，电机，残余电压可能导致芯片没断电，MCU死机，风扇慢慢转
### 主动泄放电路图
![泄放电路](image.png)  
R62和R63并联阻值变500Ω，功率容量翻倍  
R66将栅极下拉到地，默认关闭MOS，防止悬空   
R65可以与MOS的栅极电容充电，减小瞬间电流  
放电速度
$$
\tau = RC
$$
假设输出电容$C=1000μF$,泄放电阻$R=500Ω$,时间常数$\tau=0,5s$,输出24V   
最大泄放电流 
$$
I=\frac{24}{500}=48mA  
$$
最大泄放功率
$$
P=\frac{24^2}{500}=1.15W
$$
## PD诱骗
[CH224A芯片手册](https://www.semiee.com/file2/503651f5692758e97cb3b3756c6d29b4/WCH/WCH-CH224DS1_V2.0.PDF)  
![PD诱骗](image-1.png)  
## 双输入保护 
[MX5050T](https://www.semiee.com/file2/fcaa327a011006ce89bff922fd22cdea/Maxinmicro/Maxinmicro-MX5050T-DS-2301-V13.pdf)
![双输入保护电路](image-2.png)  
1. 具体工作流程  
   1. 一端上电后MOS管体二极管(寄生二极管)导通，VBUS电位上升   
   2. MX5050T检测压差，$V_{IN}-V_{OUT}>30mV$时开始给MOS栅极充电  
   3. MX5050T内有电荷泵将MOS管G级拉高，$V_{GS}>V_{th}$，MOS管导通  
   4. 原来体二极管的压降为0.7V，压降消失(电流从体二极管转向MOS沟道)   
2. 防倒灌电流     
假设VBUS被DC拉到5V，左端的$OUT>IN$，即$V_{OUT}-V_{IN}>28mV$,芯片关闭MOS