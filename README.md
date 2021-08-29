# STM32F767ZI Nucleo Ethernet Tests

## Configure project
Select board
RCC >> HSE set to Crystal/Ceramic Resonator, LSE Set to Disable
Go to clock configuration, check input frequency is 8MHz, change HCLK to 216MHz (Max)
Connectivity >> I2C1, enable as I2C (used for reading/writing EEPROM). Change SCL from PB6 to PB8 (left click PB8 on MCU diagram and select I2C1_SCL)
Connectivity >> SPI1, enable as Full-Duplex Master (for reading/writing ADC)
Connectivity >> Eth (check GPIOs are correct, should be if selected the correct board), mode RMII
-- note for some boards there is a complex memory setup procedure, doesnt seem to be the case for this one
Middleware LWIP - check enabled
Optional - change Key Options >> Heap MEM_SIZE to 10*1024 Byte(s)
Ensure that Checksum >> CHECKSUM_BY_HARDWARE is enabled 
Cortex M7 >> Enable Instruction Prefetch, CPU ICache and CPU DCache

Default memory setup should work ok, but more advanced setup covered here:
https://www.youtube.com/watch?v=Wg3edgNUsTk
Requires editing Check LWIP>>Target>>ethernetif.c and XXXX_Flash.Id in the root directory
Build Project, check "Build Analyzer" and select the "Memory Details" sub tab. This will show the memory locations for the buffers and the DMA descriptors.

## Add code

Add the following to main.c:

```C
/* USER CODE BEGIN 0 */

extern struct netif gnetif;

/* USER CODE END 0 */
```

```C
    /* USER CODE BEGIN 3 */
	  ethernetif_input(&gnetif);
	  sys_check_timeouts();
```

Build and deploy the code.

**The device get an IP address via DHCP and will now be pingable.**

To set a dynamic host name (Note this runs, but not sure whether it works, still need to check DHCP exchange in Wireshark):

Modify LWIP>>Target>>lwipopts.h to add:

```C
/* USER CODE BEGIN 0 */
#define LWIP_NETIF_HOSTNAME 1
/* USER CODE END 0 */
```

Then add the following to main.c:

```C
  /* USER CODE BEGIN 2 */
  netif_set_hostname(&gnetif, "My Custom Board");
  /* USER CODE END 2 */
```

Note there are other useful functions in Middlewares>>Third_Party>>LwIP>>src>>include>>lwip>>netif.h