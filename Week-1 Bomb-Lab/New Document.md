#BombLab实验分析
- **实验整体分析**

实验是基于Linux x86-64的，其中bomb文件是一个64位的可执行程序，bomb.c文件是一个C语言程序，其中包含了main函数以及其他许多函数。该实验共包含**六个关卡(Phase_1-Phase_6)**，每一关需要我们正确的输入密码，才能不引爆炸弹，并进行下一关。

这就需要需要我们通过对可执行文件**bomb**进行逆向分析，在其中寻找出正确答案，从而过关。此实验需要汇编基础，并配合gdb完成。（不会用gdb的请看我的实验指南文件，里面有gdb命令文档）

- **Phase_1分析**

我们使用`gdb bomb`命令进入程序，进入后在phase_1的函数入口处打上断点 命令为：`b phase_1`，进入后输入`r`是程序运行会出现如下语句：`Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!`即表示运行成功。

此时我们随意输入任意值，便会触发断点。会出现一大堆东西，看着很复杂，但是我们不用慌，此时我们输入`disassemble phase_1`这个命令，便可以对phase_1进行反汇编，会在屏幕上显示出phase_1的汇编代码：


**0x0000000000400ee0 <+0>:     sub    rsp,0x8**

**0x0000000000400ee4 <+4>:     mov    esi,0x402400**

**0x0000000000400ee9 <+9>:call 0x401338(strings_not _equal)**

**0x0000000000400eee <+14>:    test   eax,eax**

**0x0000000000400ef0 <+16>:    je     0x400ef7 <phase_1+23>**

**0x0000000000400ef2 <+18>:    call 0x40143a (explode_bomb)**

**0x0000000000400ef7 <+23>:    add    rsp,0x8**

**0x0000000000400efb <+27>:    ret**


这里我们可以发现，由于是64位，

- 第一行的栈顶指针**从esp变为了rsp**

- 第二行对esi（既rsi的低32位）赋了一个地址：**0x402400**，由于是64位的程序，前六个参数在寄存器中，所以**rsi的低32位（既esi的值）**作为参数传给了后面的`strings_not_equal`的函数

- 在第三行call了一个函数，该函数名为`strings_not_equal`，可以使用命令`disassemble strings_not_equal`查看该函数汇编，可以发现该函数是用来比较**用户输入的字符串和esi中的值是否相等**，若相等则过关，不相等就会引爆炸弹

分析到这里，第一关的答案就很明显了，第一关我们需要的密码就是**esi中的那个地址中（0x402400）存放的值**，那我们使用命令`x/s 0x402400`即可查看其中的值

**Phase_1答案**：Border relations with Canada have never been better.

- **Phase_2分析**

我们此时继续输入`disassemble phase_2`命令来对Phase_2进行反汇编，汇编代码为：

```
0x0000000000400efc <+0>:     push   rbp
   0x0000000000400efd <+1>:     push   rbx
   0x0000000000400efe <+2>:     sub    rsp,0x28
   0x0000000000400f02 <+6>:     mov    rsi,rsp
   0x0000000000400f05 <+9>:     call   0x40145c <read_six_numbers>
   0x0000000000400f0a <+14>:    cmp    DWORD PTR [rsp],0x1
   0x0000000000400f0e <+18>:    je     0x400f30 <phase_2+52>
   0x0000000000400f10 <+20>:    call   0x40143a <explode_bomb>
   0x0000000000400f15 <+25>:    jmp    0x400f30 <phase_2+52>
   0x0000000000400f17 <+27>:    mov    eax,DWORD PTR [rbx-0x4]
   0x0000000000400f1a <+30>:    add    eax,eax
   0x0000000000400f1c <+32>:    cmp    DWORD PTR [rbx],eax
   0x0000000000400f1e <+34>:    je     0x400f25 <phase_2+41>
   0x0000000000400f20 <+36>:    call   0x40143a <explode_bomb>
   0x0000000000400f25 <+41>:    add    rbx,0x4
   0x0000000000400f29 <+45>:    cmp    rbx,rbp
   0x0000000000400f2c <+48>:    jne    0x400f17 <phase_2+27>
   0x0000000000400f2e <+50>:    jmp    0x400f3c <phase_2+64>
   0x0000000000400f30 <+52>:    lea    rbx,[rsp+0x4]
   0x0000000000400f35 <+57>:    lea    rbp,[rsp+0x18]
   0x0000000000400f3a <+62>:    jmp    0x400f17 <phase_2+27>
   0x0000000000400f3c <+64>:    add    rsp,0x28
   0x0000000000400f40 <+68>:    pop    rbx
   0x0000000000400f41 <+69>:    pop    rbp
   0x0000000000400f42 <+70>:    ret
```