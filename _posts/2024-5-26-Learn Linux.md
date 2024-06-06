---
layout: post
title:  "Learn linux driver"
tags:   Linux
date:   2024-05-26 10:27:35 +0800
categories: [Linux] 
---

# Linux debug method

- Printk
- dump_stack()
    ```C
    #include <linux/module.h>
    #include <linux/kernel.h>
    static int __init helloworld_init(void)
    {
    printk(KERN_EMERG "helloworld_init\r\n");
    dump_stack();
    return 0;
    }
    static void __exit helloworld_exit(void)
    {
    printk(KERN_EMERG "helloworld_exit\r\n");
    }
    module_init(helloworld_init);
    module_exit(helloworld_exit);
    MODULE_LICENSE("GPL v2");
    MODULE_AUTHOR("topeet");
    ```
<div align=center> <img src=/assets/imgs/image-1.png  align=center/> <h6 align=center> Figure 1</h6></div>
  
- WARN_ON
    ```C
    WARN_ON(1); //(Condition)
    ```

- BUG_ON
   ```C
    BUG_ON(1); //(Condition)
   ```

- panic(fmt...)
    
    容易死机
    ```C
    panic("!!!!!");
    ```

# Learn Device tree

- **Node name**
**node-name@unit-address**
    <div align=center> <img src=/assets/imgs/2024-05-30-10-40-36.png  align=center/> <h6 align=center> Figure 2: Valid characters for node names</h6></div>
    <div align=center> <img src=/assets/imgs/2024-05-30-10-46-42.png  align=center/> <h6 align=center> Figure 3: Example of node names</h6></div>
    
    - Node 无寄存器，一定要忽略unit-address
    - 同名Node, 一定要有不同的unit-address

    **Recommand node names:**
• adc
• accelerometer
• atm
• audio-codec
• audio-controller
• backlight
• bluetooth
• bus
• cache-controller
• camera
• can
• charger
• clock
• clock-controller
• compact-flash
• can
• cpu
• cpus
• crypto
• disk
• display
• dma-controller
• dsp
• eeprom
• efuse
• endpoint
• ethernet
• ethernet-phy
• fdc
• flash
• gpio
• gpu
• gyrometer
• hdmi
• i2c
• ide
• interrupt-controller
• isa
• keyboard
• key
• keys
• lcd-controller
• led
• leds
• led-controller
• light-sensor
• magnetometer
• mailbox
• mdio
• memory
• memory-controller
• mmc
• mmc-slot
• mouse
• nand-controller
• nvram
• oscillator
• parallel
• pc-card
• pci
• pcie
• phy
• pinctrl
• pmic
• pmu
• port
• ports
• pwm
• regulator
• reset-controller
• rtc
• sata
• scsi
• serial
• sound
• spi
• sram-controller
• ssi-controller
• syscon
• temperature-sensor
• timer
• touchscreen
• usb
• usb-hub
• usb-phy
• video-codec
• vme
• watchdog
• wifi

- **Properties**
    <div align=center> <img src=/assets/imgs/2024-05-30-10-51-43.png  align=center/> <h6 align=center> Figure 4: Valid characters for properties names </h6></div>


- **Standard Properties**
    - **compatible**
      - format:   **manufacture,model**
      - value type: **stringlist**
      - eg: **compatible = "fsl,mpc9641"**

    - **model**
      - format:   **manufacture,model**
      - value type: **string**
      - eg: **model = "fsl,MPC8349EMITX"**
    
    - **phandle**
      - value type: **u32**
      - eg: 
      ```C
      pic@10000{
        phandle = <1>;
        interrupt-controller;
      };
      node1{
        interrupt-parent= <1>;
      };
      ```
   
    - **status**
        - value type: **string**
        - value: "okay" "disable" "fail" "fail-sss"
    
    - **#address-cells,#size-cells**
        - value type: **u32**
        - default value: 2,1
        - address **reg** property
        - eg: 
        ```C
        soc {
            #address-cells = <1>;
            #size-cells =<1>;
            serial {
                compatible = "node";
                reg = <0x4600 0x100>;
                clock-frequency = <0>;
                interrupts = <0xA 0x8>;
                interrupt-parent = <&ipic>;
            };
        };
        ```
    - **reg**
        - format:   **manufacture,model**
        - value type: **prop-encoded-array**
        - eg: **reg = <0x3000 0x20 0xFE00 0x100>**

    - **virtual-reg**
        - value type: **u32**

    - **ranges**
        - value type: **prop-encoded-array** or **empty**
        - eg: 
        ```C
        soc {
                compatible = "simple-bus";
                #address-cells = <1>;
                #size-cells = <1>;
                ranges = <0x0 0xe0000000 0x00100000>;
                serial {
                    device_type = "serial";
                    compatible = "ns16550";
                    reg = <0x4600 0x100>;
                    clock-frequency = <0>;
                    interrupts = <0xA 0x8>;
                    interrupt-parent = <&ipic>;
                };
        };
        ```
    
    - **dma-ranges**
        - value type: **prop-encoded-array** or **empty**
    
    - **name** (deprecated)
       - value type: **string**
    
    - **devide_type** (deprecated)
       - value type: **string**

- **Interrupts**

    - **interrupts**
        - value type: **prop-encoded-array**
        - eg: **interrupts = <0xA 8>**
    
    - **interrupt-parent**
        - value type: **phandle**

    - **interrupt-extended**
        - value type: **phandle** **<prop-encoded-array>**
        - eg: **interrupts-extended = <&pic 0xA 8>, <&gic 0xda>;**
    
    - **interrupt-cells**
        - value type: **u32**

    - **interrupt-controller**
        - value type: **empty**

- **Interrupts Nexus**
    
    - **interrupt-map**
        - value type: **prop-encoded-array**
        - format: **<child_unit_address child_interrupt_specifier interrupt_parent parent_unit_address parent_interrupt specifier>**
        - eg: 
            ```C
            open-pic {
                clock-frequency = <0>;
                interrupt-controller;
                #address-cells = <0>;
                #interrupt-cells = <2>;
            };
            pci {
                #interrupt-cells = <1>;
                #size-cells = <2>;
                #address-cells = <3>;
                interrupt-map-mask = <0xf800 0 0 7>;
                interrupt-map = <
                /* IDSEL 0x11 - PCI slot 1 */
                0x8800 0 0 1 &open-pic 2 1 /* INTA */
                0x8800 0 0 2 &open-pic 3 1 /* INTB */
                0x8800 0 0 3 &open-pic 4 1 /* INTC */
                0x8800 0 0 4 &open-pic 1 1 /* INTD */
                /* IDSEL 0x12 - PCI slot 2 */
                0x9000 0 0 1 &open-pic 3 1 /* INTA */
                0x9000 0 0 2 &open-pic 4 1 /* INTB */
                0x9000 0 0 3 &open-pic 1 1 /* INTC */
                0x9000 0 0 4 &open-pic 2 1 /* INTD */
                >;
            };
            ```
    - **interrupt-map-mask**
        - value type: **prop-encoded-array**
        
    - **#interrupt-cells*
        - value type: **u32**
- **Device Node**
    - **Base Device Node**
        - one `/cpus` node
        - 至少一个 `/memory` node
    - **Root node**
        - **#address-cells**
        - **#size-cells**
        - **model**
        - **compatible**
            "manufacturer,model"
    - **/aliases node**
        - format: **0-9,a-z,-**
        - eg:
            ```C
            aliases {
                serial0 = "/simple-bus@fe000000/serial@llc500"
            };
            ```
    - **/memory node**
        - requires: *device_type*, *reg*
        - default: #address-cells=2 #size-cells=2
        - eg:
            ```Shell
                # start address 0x0, length 0x80000000(2GB)
                memory@0 {
                    device_type = "memory";
                    reg=<0x00000000 0x00000000 0x00000000 0x80000000
                    0x00000001 0x00000000 0x00000001 0x00000000>
                };
                # start address 0x100000000, length 0x100000000(4GB)
                memory@0 {
                    device_type = "memory";
                    reg=<0x00000000 0x00000000 0x00000000 0x80000000>
                };
                memory@100000000 {
                    device_type = "memory";
                    reg=<0x00000001 0x00000000 0x00000001 0x00000000>
                };
            ```
    - **/chosen node**
        - eg:
            ```shell
                chosen {
                    bootargs = "root=/dev/nfs rw nfsroot=192.168.1.1 console=ttyS0,115200";
                };
            ```
    - **/cpus**
        - eg:
            ```shell
                cpus {
                    #address-cells = <1>;
                    #size-cells = <0>;
                    cpu@0 {
                        device_type = "cpu";
                        reg =<0>;
                        d-cache-block-size = <32>;
                        i-cache-block-size = <32>;
                        d-cache-size = <0x8000>;
                        i-cache-size =<0x8000>;
                        timebase-frequency = <82500000>;
                        clock-frequency =<82500000>;
                    };
                };
            ```





    
