## PID控制器
![pid](https://github.com/user-attachments/assets/e40d04c6-1d3c-4188-8991-fb4581bcabc9)
## 控制系统的组成结构
![pid控制系统的组成结构](https://github.com/user-attachments/assets/eab74eb2-3fbc-4f9f-98a1-78cefb3f8b4f)
## 控制系统的关键指标
![控制系统的关键指标](https://github.com/user-attachments/assets/32a3eaf8-98a0-48c3-a86f-7b58fdd8d462)
## Kp
```C
误差值：error=目标值-实际值
error*Kp
Kp的作用：让实际值不断地的接近目标值
```
## Ki
```C
Ki*(error1+error2+error3+...+errorn)即：Ki*误差的累加值
Ki的作用：用于追求更精确的控制
```
## Kd
```C
Kd*(error_now-error_last)即：Kd*（两次误差之差）
Kd作用：抑制过冲现象
```
## 积分限幅
```C
当积分项（误差累加和）超过上限时，强制其等于上限；低于下限时，强制其等于下限
即：下限=<error0+error1+error2+error3+...+errori<=上限
```
## 积分分离
```C
当 |error|> 常数C，不会参与（error0+error1+error2+...+errori）的计算
```
## 变速积分
![变速积分](https://github.com/user-attachments/assets/4d9d1132-abeb-49f3-9e8c-afa818a44c73)
## 微分先行
- nowi-nowi-1表示：这次实际值与上次实际值之差
<img width="554" height="251" alt="微分先行" src="https://github.com/user-attachments/assets/6edcd135-cf16-4296-82b3-f051a27efc72" />
## 输出限幅
```C
|error*Kp+Ki*误差的累加值+Kd*（两次误差之差）|<=常数C
```
