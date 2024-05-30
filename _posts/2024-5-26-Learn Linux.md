<!--
 * @description: 
 * @param: 
 * @Date: 2024-05-30 10:33:03
 * @LastEditTime: 2024-05-30 10:33:47
 * @LastEditors: Yuxi <Yuxi.Wang@monolithicpower.com>
-->
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

    ![alt text](/assets/imgs/image-1.png)

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