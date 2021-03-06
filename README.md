# STM32F767ZI Nucleo Ethernet Tests

## Configure project

- Select board
- RCC >> HSE set to Crystal/Ceramic Resonator, LSE Set to Disable
- Go to clock configuration, check input frequency is 8MHz, change HCLK to 216MHz (Max)
- Connectivity >> ETH (check GPIOs are correct, should be if selected the correct board), mode should be RMII
- For some boards there is a complex memory setup procedure, doesnt seem to be the case for this one (see youtube link below)
- Middleware LWIP - check enabled
- Optional - change Key Options >> Heap MEM_SIZE to 10*1024 Byte(s)
- Ensure that Checksum >> CHECKSUM_BY_HARDWARE is enabled 
- Cortex M7 >> Enable Instruction Prefetch, CPU ICache and CPU DCache

Default memory setup should work ok, but more advanced setup covered here: https://www.youtube.com/watch?v=Wg3edgNUsTk (Requires editing LWIP>>Target>>ethernetif.c and XXXX_Flash.Id in the root directory)

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

To set a dynamic host name:

Modify LWIP>>Target>>lwipopts.h to add:

```C
/* USER CODE BEGIN 0 */
#define LWIP_NETIF_HOSTNAME 1
/* USER CODE END 0 */
```

Then add the following to main.c:

```C
  /* USER CODE BEGIN 2 */
  netif_set_hostname(&gnetif, "MyBoard");
  /* USER CODE END 2 */
```

Note there are other useful functions in Middlewares>>Third_Party>>LwIP>>src>>include>>lwip>>netif.h
