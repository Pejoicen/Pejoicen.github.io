---
title: "Verilog 常用语法"
date: 2018-12-01T23:39:32+08:00
description: ""
draft: false
tags: ["Verilog","语法"]
categories: [FPGA]
---

整理Verilog 常用语法

<!--more-->
```verilog
/******************************************************************************/
// 参考《verilog运算符》:https://blog.csdn.net/Eric_Sun_/article/details/78055417 
/*******************************************************************************/


// 1.Arithmetic Operations 算术运算符: 
// + 加
// - 减
// * 乘
// / 除
// % 余 5%2=1; 5%-2=1; -5%2=-1; -5%-2=-1

// 2.Bitwise Operations 位运算符: 输入几位就输出几位，若两侧位宽不一致则自动在左侧补0
// ~  invert
// &  and
// |  or
// ^  Exclusive OR 异或
// ~^ Exclusive NOR  <=> ^~

// 3. Logical Operations 逻辑运算符 输出Ture/False 1bit
// && 逻辑与 
// || 逻辑或 
// !  逻辑非 0为Fale 其他为True，在此基础上做非操作

// 4. Reduction Operators 缩减运算符 逐个bit做运算,输出Ture/False结果 1bit
// &  and                   // & 是VHDL总位拼接的运算符号
// ~& not and
// |  OR
// ~| not OR
// ^  Exclusive OR  异或
// ~^ Exclusive NOR
// ^~ Exclusive NOR
* signal = 1'b0 or 1'b1

// 5.Relational Operators 关系运算符 输出Ture/False 1bit
// <，>，<=，>=，==，!=，
// ===，!==


// 6.Conditional Operator 条件运算 
// assign A = B ? C : D;    // if B is true A = C else A = D

// 7. Concatenation Operator 串联运算 // 位拼接
assign signal0 = {signal1,signal2}// {,}    & 是VHDL总位拼接的运算符号          

// 8. Replication Operator 复制操作符 {NUM{1'b1}}来赋值，NUM是位宽。
wire   A = 1'b1; B = 2'b00; C = 2'b10; D= 3'b110;
assign Y = { 4{A} }; // Results in Y =4’b1111 
assign Y = { 4{A}, 2{B} }; // Results in Y = 8’b11110000
assign Y = { 4{A}, 2{B}, C }; // Resultsin Y = 10’b1111000010


/*******************************************************************************/
/*******************************************************************************/
assign signal0 = 4'b111;    // 4'd15 = 4'hf                                     // 不同进制赋值



//verilog中for循环的用法、case用法 
genvar i;
generate
for (i=0;i<8;i=i+1) begin
    always@(posedge CpSl_Clk_i) begin
        if (CpSl_RstN_i == 1'b0) begin
            PrSv_Reg0_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg1_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg2_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg3_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg4_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg5_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg6_s[i*8+7:i*8]     <= 'b0;
            PrSv_Reg7_s[i*8+7:i*8]     <= 'b0;
        end
        else if (AMBA_SLAVE_0_WVALID_S0 == 1'b1 && AMBA_SLAVE_0_WSTRB_S0[i]) begin 
            case (PrSv_AWADDR_s[5:3])
            3'b000: begin
                PrSv_Reg0_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b001: begin
                PrSv_Reg1_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end            
            3'b010: begin
                PrSv_Reg2_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b011: begin
                PrSv_Reg3_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b100: begin
                PrSv_Reg4_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b101: begin
                PrSv_Reg5_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b110: begin
                PrSv_Reg6_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            3'b111: begin
                PrSv_Reg7_s[i*8+7:i*8] <= AMBA_SLAVE_0_WDATA_S0[i*8+7:i*8];
            end
            default:;
            endcase
        end
    end
end
endgenerate

```
