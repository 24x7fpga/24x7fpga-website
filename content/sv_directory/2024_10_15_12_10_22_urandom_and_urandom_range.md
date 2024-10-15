+++
title = "$urandom and $urandom_range"
author = ["Kiran"]
date = 2024-10-15T12:10:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

In SystemVerilog, `$urandom` and `$urandom_range` are system functions used to generate pseudo-random integers. It’s especially useful in testbenches and verification environments where randomization is essential to stimulate various input scenarios for DUT (Design Under Test).


## $urandom {#urandom}

-   Returns a 32-bit unsigned random number each time it is called.
-   It uses a seed to initialize the random number generator, and each call to `$urandom` produces a different value based on this seed.
-   If you use the same seed, the random numbers will follow the same sequence, which is useful for debugging. Without explicit seeding, `$urandom` uses an automatic seed.


### Example {#example}

```verilog
int rn;
rn = $urandom;       // automatic seed
rn = $urandom(24);   // seed with a specific values
rn = $urandom % 10;  // random value between 0 and 9
```


## $urandom_range {#urandom-range}

-   The function `$urandom_range(min,max)` returns an unsigned integer within the specified range (min, max).


### Example {#example}

```verilog
int rn;
rn = $urandom(10,20); // generates random values between 10 and 20
```


## Points to Remember {#points-to-remember}

1.  Both `$urandom` and `$urandom_range` functions generate only 32-bit integers.
2.  To create a random value larger than 32 bits, you can concatenate two 32-bit random numbers.
