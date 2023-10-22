# NOP Sled

## What is it?

A NOP-sled is the oldest and most widely known technique for exploiting stack buffer overflows.
It solves the problem of finding the exact address of the buffer by effectively increasing the 
size of the target area. To do this, much larger sections of the stack are corrupted with the
no-op machine instruction.

### No-Operation

For example, in the `x86` architecture, the canonical NOP instruction is represented by the byte `0x90`. 
It essentially tells the processor to do **nothing** and move to the next instruction.

## Why use it?

The idea behind **NOP-slides** is to increase the attacking surface for a shellcode injection. 

``` text
Bad Metaphorical Example:
For instance, instead of writing "jump to point 0.214881212" we could write 
"jump somewhere between 0.22 and 1.0" and if we landed on "0.5" we would just 
slide down to "0.214881212" as all the instructions between "0.214881212" and "1.0"
are just "go to the next one".
```

What I am trying to convey here: Instead of finding a needle in the haystack, we can just bring a magnet,
which allows us to not having to look for a small point but just need to look in larger areas. 

``` text
Stack Memory
---------------------------------------
|  ...  | <- Some other data
---------------------------------------
| 0x90  | <- NOP sled begins (Call it Pass1)
---------------------------------------
| 0x90  | (Call it Pass2)
---------------------------------------
| 0x90  | (Call it Pass3)
---------------------------------------
|  ...  | 
---------------------------------------
| 0x90  | <- NOP sled ends
---------------------------------------
|  Shellcode | <- malicious code goes here :)))
---------------------------------------
```

If we set our return address to `Pass2` the processor would slide all the way down until we reached
the `0xMalicous` address, at which point we would execute the code. 

It's like a slippery slide, if we touch any part of it, we slide down to the malicous code.







