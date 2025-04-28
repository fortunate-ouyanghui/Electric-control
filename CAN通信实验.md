## 使用CAN通信实现DS0和DS1流水灯现象（can发送数据data，can同时接收data对其进行解析来控制led）
## cubemax配置
- SYS配置
![sys](https://github.com/user-attachments/assets/4d1ed2f9-5515-4fba-90ba-a8e1045eaff6)
- RCC配置
![rcc](https://github.com/user-attachments/assets/38eec99d-61cb-431f-aaa1-21619fa352e0)
- 时钟树配置
![clk](https://github.com/user-attachments/assets/226d841f-60d0-4ed3-8edf-98cf1eeec4ca)
- can配置（选择回环模式，开启接收中断）
![can1](https://github.com/user-attachments/assets/326e59bb-ccaa-49f1-841c-1683150f1a9d)
![can2](https://github.com/user-attachments/assets/2e80417d-d90e-4b1f-9c34-62754ff79c6b)
 ## API
 - HAL_CAN_Start(CAN_HandleTypeDef *hcan);//开启can控制器，CAN 控制器会开始监听hcan总线
 - __HAL_CAN_ENABLE_IT(CAN_HandleTypeDef *hcan,CAN_IT_RX_FIFO1_MSG_PENDING);使能hcan这个can控制器的FIFO1消息挂起中断，当有数据发送到FIFO1时，就会调用HAL_CAN_RxFifo1MsgPendingCallback这个回调函数（注意：只需使能一次就行）
 - HAL_CAN_AddTxMessage (CAN_HandleTypeDef *hcan, const CAN_TxHeaderTypeDef *pHeader, const uint8_t aData[], uint32_t *pTxMailbox)；
作用：该函数会控制CAN控制器通过hcan，发送消息头为pHeader，数据为aData的消息，并将实际使用的发送邮箱编号存放在pTxMailbox中
- CAN_FilterTypeDef can_filter_init_structure;//配置滤波器的结构体
