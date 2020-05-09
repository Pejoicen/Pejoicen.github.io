---
title: "Zynq GPIO"
date: 2019-08-22T19:37:10+08:00
description: ""
draft: false
tags: ["ZYNQ","嵌入式","C/C++"]
categories: ["软件"]
---

详细整理Zynq GPIO初始化，配置，以及中断的用法。整理的过程中，熟悉Xilinx的命名、使用习惯。

<!--more-->



# GPIO  

初始化GPIO
配置GPIO
设置GPIO中断

## GPIO初始化

1. 声明 XGpioPs

> 作用：作为GPIO驱动的一个实例？
```c
static XGpioPs Gpio; /* The Instance of the GPIO Driver */

-----------------------------------------------------------------------
/**
 * The XGpioPs driver instance data. The user is required to allocate a
 * variable of this type for the GPIO device in the system. A pointer
 * to a variable of this type is then passed to the driver API functions.
 */
typedef struct {
	XGpioPs_Config GpioConfig;	/**< Device configuration */
	u32 IsReady;			/**< Device is initialized and ready */
	XGpioPs_Handler Handler;	/**< Status handlers for all banks */
	void *CallBackRef; 		/**< Callback ref for bank handlers */
	u32 Platform;			/**< Platform data */
	u32 MaxPinNum;			/**< Max pins in the GPIO device */
	u8 MaxBanks;			/**< Max banks in a GPIO device */
} XGpioPs;

```

2. 声明 并 获取 XGpioPs_Config

> 作用：声明GPIO的配置信息变量，查找 配置信息

```c
	XGpioPs_Config *ConfigPtr;
    int Status = 0;

	//printf("Initialize the Gpio driver.\n\r");
	ConfigPtr = XGpioPs_LookupConfig(GPIO_DEVICE_ID);
	if (ConfigPtr == NULL) {
		return XST_FAILURE;
	}

-----------------------------------------------------------------------
/**
 * This typedef contains configuration information for a device.
 */
typedef struct {
	u16 DeviceId;		/**< Unique ID of device */
	u32 BaseAddr;		/**< Register base address */
} XGpioPs_Config;

-----------------------------------------------------------------------
/*****************************************************************************/
/**
* 根据ID号，在配置表中找到并返回对应的GPIO设备信息，函数返回类型为自定义类型的指针。
* This function looks for the device configuration based on the unique device
* ID. The table XGpioPs_ConfigTable[] contains the configuration information
* for each device in the system.
*
* @param	DeviceId is the unique device ID of the device being looked up.
*
* @return	A pointer to the configuration table entry corresponding to the
*		given device ID, or NULL if no match is found.
*
* @note		None.
*
******************************************************************************/
XGpioPs_Config *XGpioPs_LookupConfig(u16 DeviceId)
{
	XGpioPs_Config *CfgPtr = NULL;
	u32 Index;

	for (Index = 0U; Index < (u32)XPAR_XGPIOPS_NUM_INSTANCES; Index++) {
		if (XGpioPs_ConfigTable[Index].DeviceId == DeviceId) {
			CfgPtr = &XGpioPs_ConfigTable[Index];
			break;
		}
	}

	return (XGpioPs_Config *)CfgPtr;
}


/* GPIO配置表
* The configuration table for devices
*/

XGpioPs_Config XGpioPs_ConfigTable[XPAR_XGPIOPS_NUM_INSTANCES] =
{
	{
		XPAR_PS7_GPIO_0_DEVICE_ID,
		XPAR_PS7_GPIO_0_BASEADDR
	}
};



```

3. 配置GPIO方向、读写GPIO

```c

    XGpioPs_SetDirection(&Gpio, GPIO_BANK, GPIO_DIR);    // bank3 set to all input

    // 函数在 xgpiops.c 中，其中还包括了配置GPIO方向，以及写、读GPIO引脚的功能函数


-----------------------------------------------------------------------
/****************************************************************************/
/**
*
* Set the Direction of the pins of the specified GPIO Bank.
*
* @param	InstancePtr is a pointer to the XGpioPs instance.
* @param	Bank is the bank number of the GPIO to operate on.
*		Valid values are 0-3 in Zynq and 0-5 in Zynq Ultrascale+ MP.
* @param	Direction is the 32 bit mask of the Pin direction to be set for
*		all the pins in the Bank. Bits with 0 are set to Input mode,
*		bits with 1 are	set to Output Mode.
*
* @return	None.
*
* @note		This function is used for setting the direction of all the pins
*		in the specified bank. The previous state of the pins is
*		not maintained.
*
*****************************************************************************/
void XGpioPs_SetDirection(XGpioPs *InstancePtr, u8 Bank, u32 Direction)
{
	Xil_AssertVoid(InstancePtr != NULL);
	Xil_AssertVoid(InstancePtr->IsReady == XIL_COMPONENT_IS_READY);
	Xil_AssertVoid(Bank < InstancePtr->MaxBanks);

	XGpioPs_WriteReg(InstancePtr->GpioConfig.BaseAddr,
			  ((u32)(Bank) * XGPIOPS_REG_MASK_OFFSET) +
			  XGPIOPS_DIRM_OFFSET, Direction);
}


-----------------------------------------------------------------------

/****************************************************************************/
/**
*
* Read the Data register of the specified GPIO bank.
*
* @param	InstancePtr is a pointer to the XGpioPs instance.
* @param	Bank is the bank number of the GPIO to operate on.
*		Valid values are 0-3 in Zynq and 0-5 in Zynq Ultrascale+ MP.
*
* @return	Current value of the Data register.
*
* @note		This function is used for reading the state of all the GPIO pins
*		of specified bank.
*
*****************************************************************************/
u32 XGpioPs_Read(XGpioPs *InstancePtr, u8 Bank)
{
	Xil_AssertNonvoid(InstancePtr != NULL);
	Xil_AssertNonvoid(InstancePtr->IsReady == XIL_COMPONENT_IS_READY);
	Xil_AssertNonvoid(Bank < InstancePtr->MaxBanks);

	return XGpioPs_ReadReg(InstancePtr->GpioConfig.BaseAddr,
				 ((u32)(Bank) * XGPIOPS_DATA_BANK_OFFSET) +
				 XGPIOPS_DATA_RO_OFFSET);
}

-----------------------------------------------------------------------

/****************************************************************************/
/**
*
* Write to the Data register of the specified GPIO bank.
*
* @param	InstancePtr is a pointer to the XGpioPs instance.
* @param	Bank is the bank number of the GPIO to operate on.
*		Valid values are 0-3 in Zynq and 0-5 in Zynq Ultrascale+ MP.
* @param	Data is the value to be written to the Data register.
*
* @return	None.
*
* @note		This function is used for writing to all the GPIO pins of
*		the bank. The previous state of the pins is not maintained.
*
*****************************************************************************/
void XGpioPs_Write(XGpioPs *InstancePtr, u8 Bank, u32 Data)
{
	Xil_AssertVoid(InstancePtr != NULL);
	Xil_AssertVoid(InstancePtr->IsReady == XIL_COMPONENT_IS_READY);
	Xil_AssertVoid(Bank < InstancePtr->MaxBanks);

	XGpioPs_WriteReg(InstancePtr->GpioConfig.BaseAddr,
			  ((u32)(Bank) * XGPIOPS_DATA_BANK_OFFSET) +
			  XGPIOPS_DATA_OFFSET, Data);
}

```

## GPIO中断
1. 声明GIC驱动实例 XScuGic

```c

XScuGic GicInstancePtr; /* The Instance of the Interrupt Controller Driver */

--------------------------------------------------------------------------------
/**
 * The XScuGic driver instance data. The user is required to allocate a
 * variable of this type for every intc device in the system. A pointer
 * to a variable of this type is then passed to the driver API functions.
 */
typedef struct
{
	XScuGic_Config *Config;  /**< Configuration table entry */
	u32 IsReady;		 /**< Device is initialized and ready */
	u32 UnhandledInterrupts; /**< Intc Statistics */
} XScuGic;

```
2. 声明 并 查找 GIC配置信息 XScuGic_Config,XScuGic_CfgInitialize

> 功能和GPIO类似，初始化
```c

	XScuGic_Config *IntcConfig; /* Instance of the interrupt controller */
    
    IntcConfig = XScuGic_LookupConfig(INTC_DEVICE_ID);
	if (NULL == IntcConfig) {
		return XST_FAILURE;
	}
    
	Status = XScuGic_CfgInitialize(&GicInstancePtr, IntcConfig,
					IntcConfig->CpuBaseAddress);
	if (Status != XST_SUCCESS) {
		return XST_FAILURE;
	}
    
    
```
3. 注册 中断控制器 handler 和 硬件中断 handling 逻辑 Xil_ExceptionRegisterHandler

> interrupt handler = interrupt service routine ISR 中断服务程序
```c
	/*
	 * Connect the interrupt controller interrupt handler to the hardware
	 * interrupt handling logic in the processor.
	 */
	Xil_ExceptionRegisterHandler(XIL_EXCEPTION_ID_INT,                          // 5 XIL_EXCEPTION_ID_IRQ_INT
				(Xil_ExceptionHandler)XScuGic_InterruptHandler,                 // must call this func
				&GicInstancePtr);                                               // Gic instance
     
--------------------------------------------------------------------------------     
/*****************************************************************************/
/**
* @brief	Register a handler for a specific exception. This handler is being
*			called when the processor encounters the specified exception.
*
* @param	exception_id contains the ID of the exception source and should
*			be in the range of 0 to XIL_EXCEPTION_ID_LAST.
*			See xil_exception.h for further information.
* @param	Handler to the Handler for that exception.
* @param	Data is a reference to Data that will be passed to the
*			Handler when it gets called.
*
* @return	None.
*
* @note		None.
*
****************************************************************************/
void Xil_ExceptionRegisterHandler(u32 Exception_id,
				    Xil_ExceptionHandler Handler,
				    void *Data)
{
	XExc_VectorTable[Exception_id].Handler = Handler;
	XExc_VectorTable[Exception_id].Data = Data;
}

--------------------------------------------------------------------------------    
/*****************************************************************************/
/**
* This function is the primary interrupt handler for the driver.  It must be
* connected to the interrupt source such that it is called when an interrupt of
* the interrupt controller is active. It will resolve which interrupts are
* active and enabled and call the appropriate interrupt handler. It uses
* the Interrupt Type information to determine when to acknowledge the interrupt.
* Highest priority interrupts are serviced first.
*
* This function assumes that an interrupt vector table has been previously
* initialized.  It does not verify that entries in the table are valid before
* calling an interrupt handler.
*
*
* @param	InstancePtr is a pointer to the XScuGic instance.
*
* @return	None.
*
* @note		None.
*
******************************************************************************/
void XScuGic_InterruptHandler(XScuGic *InstancePtr)
{

	u32 InterruptID;
	    u32 IntIDFull;
	    XScuGic_VectorTableEntry *TablePtr;

	    /* Assert that the pointer to the instance is valid
	     */
	    Xil_AssertVoid(InstancePtr != NULL);

	    /*
	     * Read the int_ack register to identify the highest priority interrupt ID
	     * and make sure it is valid. Reading Int_Ack will clear the interrupt
	     * in the GIC.
	     */
	    IntIDFull = XScuGic_CPUReadReg(InstancePtr, XSCUGIC_INT_ACK_OFFSET);
	    InterruptID = IntIDFull & XSCUGIC_ACK_INTID_MASK;

	    if(XSCUGIC_MAX_NUM_INTR_INPUTS < InterruptID){
		goto IntrExit;
	    }

	    /*
	     * If the interrupt is shared, do some locking here if there are multiple
	     * processors.
	     */
	    /*
	     * If pre-eption is required:
	     * Re-enable pre-emption by setting the CPSR I bit for non-secure ,
	     * interrupts or the F bit for secure interrupts
	     */

	    /*
	     * If we need to change security domains, issue a SMC instruction here.
	     */

	    /*
	     * Execute the ISR. Jump into the Interrupt service routine based on the
	     * IRQSource. A software trigger is cleared by the ACK.
	     */
	    TablePtr = &(InstancePtr->Config->HandlerTable[InterruptID]);
		if(TablePtr != NULL) {
	        TablePtr->Handler(TablePtr->CallBackRef);
		}

	IntrExit:
	    /*
	     * Write to the EOI register, we are all done here.
	     * Let this function return, the boot code will restore the stack.
	     */
	    XScuGic_CPUWriteReg(InstancePtr, XSCUGIC_EOI_OFFSET, IntIDFull);

	    /*
	     * Return from the interrupt. Change security domains could happen here.
     */
}
```

4. XScuGic_Connect

```c

	/*
	 * Connect the device driver handler that will be called when an
	 * interrupt for the device occurs, the handler defined above performs
	 * the specific interrupt processing for the device.
	 */
	Status = XScuGic_Connect(&GicInstancePtr, 
                GPIO_INTERRUPT_ID,                                              // 52
				(Xil_ExceptionHandler)XGpioPs_IntrHandler,
				&Gpio);
	if (Status != XST_SUCCESS) {
		return Status;
	}
    
--------------------------------------------------------------------------------



```

/****************************************************************************/
/**
 * This handler data type allows the user to define a callback function to
 * handle the interrupts for the GPIO device. The application using this
 * driver is expected to define a handler of this type, to support interrupt
 * driven mode. The handler executes in an interrupt context such that minimal
 * processing should be performed.
 *
 * @param	CallBackRef is a callback reference passed in by the upper layer
 *		when setting the callback functions for a GPIO bank. It is
 *		passed back to the upper layer when the callback is invoked. Its
 *		type is not important to the driver component, so it is a void
 *		pointer.
 * @param	Bank is the bank for which the interrupt status has changed.
 * @param	Status is the Interrupt status of the GPIO bank.
 *
 *****************************************************************************/
typedef void (*XGpioPs_Handler) (void *CallBackRef, u32 Bank, u32 Status);

/**
 * This typedef contains configuration information for a device.
 */
typedef struct {
	u16 DeviceId;		/**< Unique ID of device */
	u32 BaseAddr;		/**< Register base address */
} XGpioPs_Config;

/**
 * The XGpioPs driver instance data. The user is required to allocate a
 * variable of this type for the GPIO device in the system. A pointer
 * to a variable of this type is then passed to the driver API functions.
 */
typedef struct {
	XGpioPs_Config GpioConfig;	/**< Device configuration */
	u32 IsReady;			/**< Device is initialized and ready */
	XGpioPs_Handler Handler;	/**< Status handlers for all banks */
	void *CallBackRef; 		/**< Callback ref for bank handlers */
	u32 Platform;			/**< Platform data */
	u32 MaxPinNum;			/**< Max pins in the GPIO device */
	u8 MaxBanks;			/**< Max banks in a GPIO device */
} XGpioPs;


# GPIO 中断

```c

	/*
	 * Run the GPIO interrupt example, specify the parameters that
	 * are generated in xparameters.h.
	 */
	Status = GpioIntrExample(&Intc, &Gpio, GPIO_DEVICE_ID,
				 GPIO_INTERRUPT_ID);




/****************************************************************************/
/**
* This function shows the usage of interrupt fucntionality of the GPIO device.
* It is responsible for initializing the GPIO device, setting up interrupts and
* providing a foreground loop such that interrupts can occur in the background.
*
* @param	Intc is a pointer to the XScuGic driver Instance.
* @param	Gpio is a pointer to the XGpioPs driver Instance.
* @param	DeviceId is the XPAR_<Gpio_Instance>_PS_DEVICE_ID value
*		from xparameters.h.
* @param	GpioIntrId is XPAR_<GIC>_<GPIO_Instance>_VEC_ID value
*		from xparameters.h
*
* @return
*		- XST_SUCCESS if the example has completed successfully.
*		- XST_FAILURE if the example has failed.
*
* @note		None
*
*****************************************************************************/
int GpioIntrExample(XScuGic *Intc, XGpioPs *Gpio, u16 DeviceId, u16 GpioIntrId)
{
	XGpioPs_Config *ConfigPtr;
	int Status;
	int Type_of_board;

	Type_of_board = XGetPlatform_Info();
	switch (Type_of_board) {
	case XPLAT_ZYNQ_ULTRA_MP:
		Input_Pin = 22;
		Output_Pin = 23;
		break;

	case XPLAT_ZYNQ:
		Input_Pin = 14;
		Output_Pin = 10;
		break;
	}

	/* Initialize the Gpio driver. */
	ConfigPtr = XGpioPs_LookupConfig(DeviceId);
	if (ConfigPtr == NULL) {
		return XST_FAILURE;
	}
	XGpioPs_CfgInitialize(Gpio, ConfigPtr, ConfigPtr->BaseAddr);

	/* Run a self-test on the GPIO device. */
	Status = XGpioPs_SelfTest(Gpio);
	if (Status != XST_SUCCESS) {
		return XST_FAILURE;
	}

	/* Set the direction for the specified pin to be input */
	XGpioPs_SetDirectionPin(Gpio, Input_Pin, 0x0);

	/* Set the direction for the specified pin to be output. */
	XGpioPs_SetDirectionPin(Gpio, Output_Pin, 1);
	XGpioPs_SetOutputEnablePin(Gpio, Output_Pin, 1);
	XGpioPs_WritePin(Gpio, Output_Pin, 0x0);

	/*
	 * Setup the interrupts such that interrupt processing can occur. If
	 * an error occurs then exit.
	 */
	Status = SetupInterruptSystem(Intc, Gpio, GPIO_INTERRUPT_ID);
	if (Status != XST_SUCCESS) {
		return XST_FAILURE;
	}

	xil_printf("\n\rPush Switch button to exit\n\r");
	AllButtonsPressed = FALSE;

	/*
	 * Loop forever while the button changes are handled by the interrupt
	 * level processing.
	 */
	while(AllButtonsPressed == FALSE);

	return XST_SUCCESS;
}


```


# 常用函数
XGpioPs_LookupConfig
XScuGic_LookupConfig