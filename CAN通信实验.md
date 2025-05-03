## 使用CAN通信实现DS0和DS1流水灯现象（can发送数据data，can同时接收data对其进行解析来控制led）
## cubemax配置
- SYS配置
![sys](https://github.com/user-attachments/assets/4d1ed2f9-5515-4fba-90ba-a8e1045eaff6)
- RCC配置
![rcc](https://github.com/user-attachments/assets/38eec99d-61cb-431f-aaa1-21619fa352e0)
- 时钟树配置
![clk](https://github.com/user-attachments/assets/226d841f-60d0-4ed3-8edf-98cf1eeec4ca)
- can配置（选择回环模式，开启接收中断）（can通信中时间片一定要设置正确）
![can1](https://github.com/user-attachments/assets/326e59bb-ccaa-49f1-841c-1683150f1a9d)
![can2](https://github.com/user-attachments/assets/2e80417d-d90e-4b1f-9c34-62754ff79c6b)
 ## API
 - 开启CAN控制器
```C
 HAL_CAN_Start(CAN_HandleTypeDef *hcan);//开启can控制器，CAN 控制器会开始监听hcan总线

示例：开启can并开启FIFO1消息挂起中断
void CAN_Start(void)
{
	HAL_CAN_Start(&hcan1);
	 //__HAL_CAN_ENABLE_IT(&hcan1, CAN_IT_RX_FIFO1_MSG_PENDING);
	HAL_CAN_ActivateNotification(&hcan1,CAN_IT_RX_FIFO1_MSG_PENDING);
}
```
- 开启CAN控制器的FIFOx消息挂起中断（只需使用一次区别于串口中断，开启后，当FIFOx收到消息时，就会调用就会调用HAL_CAN_RxFifo1MsgPendingCallback()函数）
```C
方式一：
HAL_CAN_ActivateNotification（CAN_HandleTypeDef *hcan, uint32_t ActiveITs）；第2个参数表示FIFO几。
方式二：
__HAL_CAN_ENABLE_IT(hcan, ActiveITs)；//第二个参数表示FIFO几
```
- 发送消息
```C
HAL_CAN_AddTxMessage (CAN_HandleTypeDef *hcan, const CAN_TxHeaderTypeDef *pHeader, const uint8_t aData[], uint32_t *pTxMailbox)；
作用：该函数会控制CAN控制器通过hcan，发送消息头为pHeader，数据为aData的消息，并将实际使用的发送邮箱编号存放在pTxMailbox中
示例：发送报文ID为：0x001的报文
//can1发送ID位0x001的数据，标准数据帧
void CAN_Send(uint8_t data)
{
	uint32_t usedMailbox;
	CAN_TxHeaderTypeDef Header;
	Header.StdId=0x001;//标准帧率ID
	Header.ExtId=0;//用来存放扩展的ID，扩展帧ID长度要大于标准帧ID长度
	Header.IDE=0;//表示不是扩展帧，是标准帧
	Header.DLC=1;//表示Data有多少个字节
	Header.RTR=0;//不是遥控帧
	HAL_CAN_AddTxMessage(&hcan1,&Header, &data,&usedMailbox);
}
```
- 配置滤波器
```C
HAL_StatusTypeDef HAL_CAN_ConfigFilter(CAN_HandleTypeDef *hcan, const CAN_FilterTypeDef *sFilterConfig);
示例：过滤出报文ID为：0x008的报文

//过滤出我想要的ID：0x008;
void filter()
{
	CAN_FilterTypeDef Filter;
	Filter.FilterBank=13;//使用过滤器13（can1有0-13个过滤器，can2有14-27个过滤器）
	Filter.FilterMode=CAN_FILTERMODE_IDMASK;//使用掩码模式
	Filter.FilterScale=CAN_FILTERSCALE_32BIT;//32位滤波器
	Filter.FilterFIFOAssignment=CAN_FILTER_FIFO1;//使用FIFO1
	Filter.FilterIdHigh=(0x008<<5);//高16位ID，配置想要过滤出的ID
	Filter.FilterIdLow=0;//低16位ID
	Filter.FilterMaskIdHigh=(0x7FF<<5);//高16位掩码
	Filter.FilterMaskIdLow=0;//低16位掩码
	Filter.FilterActivation=ENABLE;//使能过滤器
	
	HAL_CAN_ConfigFilter(&hcan1,&Filter);
}
```
- 回调函数
```C
void HAL_CAN_RxFifo1MsgPendingCallback(CAN_HandleTypeDef *hcan)//开启CAN控制器的FIFOx消息挂起中断后，收到消息就会进入这个函数
{
  CAN_RxHeaderTypeDef header;
  uint8_t data;
  HAL_CAN_GetRxMessage(hcan,CAN_FILTER_FIFO1,&header,&data);
}
```

