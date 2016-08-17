# Pow and multiplication

So there was a debate on the use of `pow(x,2)` in C++. It is seemed slower than `x*x`

Reference code which brought up this discussion: https://github.com/codingchallenge8/codingchallenge2016/blob/master/q4/q4.cpp

### Bit Shifts
- Bit shifts may or may not speed up a power function.
- Bit shift can be useful for integers but not for doubles
- For high performance computing for high level programming languages such as C++, the compiler will be smart enough to optimise and generate the correct code for the correct target
- SHRD (Double Precision Shift Right) takes 4 clock cycles + 1 clock cycle for latency time, while IMULT takes 1 clock cycle
- Reference: http://www.agner.org/optimize/instruction_tables.pdf

On another note,
0xFFFF FFFF is -1. When you do a right shift, you get 0x7FFF FFFF which is wrong. Remember than LMB is the signed bit. So same for 0x7FFF FFFF, when you shift left, you get 0xFFFF FFFE. So be careful when you bit shift.

### pow(x,2) vs x*x

#### pow is an inline function
- This is similar to things like std::vector()::size()
- This is a compiler optimisation that requires only a single instruction on most architectures
- Likewise, x*x is IMULT which requires only a single instruction

### Decompiling code
Decompiled with Visual Studio 2010. Put a break point, and upon break, press CTRL-ALT-D.

```
pow(x,2)

00A1106D  push        2  
00A1106F  sub         esp,8  
00A11072  fld         qword ptr [x]  
00A11075  fstp        qword ptr [esp]  
00A11078  call        pow (0A11005h)  
00A1107D  add         esp,0Ch  
00A11080  fstp        qword ptr [x]  
```


```
x*x

0108106D  fld         qword ptr [x]  
01081070  fmul        qword ptr [x]  
01081073  fstp        qword ptr [x]  
```

So the difference between these instructions is FMUL and POW call. Now we have determined that `pow` is an inline function, and `FMUL` also takes 1 instruction. 

So running these functions 1 million times show no significant difference. Both are around 0.337 and 0.339, averaging 2 ms difference. It was run on i7, Visual Studio 2010 Professional.

### function declaration of pow
It is mentioned that there is no function declaration for `pow(double, int)`, only `pow(double, double)` and hence there is a conversion cost from int to double.

However, this is not removed. It is just overloaded.

More about: http://stackoverflow.com/questions/5627030/why-was-stdpowdouble-int-removed-from-c11

THerefore, both `pow(x,2)` and `x*x` should run about the same time and be optimised by the compiler

### Other findings
- Sqrt is very fast with only about 4-7 clock cycles
