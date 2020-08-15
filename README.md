# numc
This is an overview of the steps I took to complete my final project in CS61C.

**Project: https://cs61c.org/su20/projects/proj4/**

Naïve implementations of matrix algorithms can become extraordinarily slow (especially for large matrices).
The aim of this project was to recreate the popular Python library, numpy, except in C, with a special eye out for optimization.
While this solution is not quite as fast as numpy itself, it still outperforms naive implementations rather well.

This project was split into 5 parts as follows:
1. Write (naïve) matrix functions in C (matrix.c)
2. Writing a setup file to install custom modules into Python (setup.py)
3. Writing the Python-C interface (numc.c)
4. Optimization (matrix.c)
5. Writing the ramble.md file

Here, I will discuss mostly step 4, since the first three steps, though certainly time-consuming and interesting in their own right, aren't really the main focus of this project.

We covered several optimization methods throughout the course, including loop unrolling, using SSE/AVX Intel intrinsics, and Open MP.
We also learned to exploit caching and concepts like spatial locality, that are especially useful for multiplying matrices.
Finally, we were also tasked with creatively using faster algorithms wherever possible.

Grading for the optimization portion of the project was as follows:
  Minimum speedup for 100%:

    Multiplication: 95x
    Power: 1900x
    Simple: 5x
    Comprehensive: 98x

Simple included the functions Add, Sub, Neg, and Abs. Comprehensive was a wholistic look at all the functions.

My autograder results are as follows:

    Test Name               | Speedup     | Score   | Points  |
    Multiply Benchmark      | 126.663543x | 1 / 1.0 | 8 / 8   |
    Power Benchmark         | 2080.41831x | 1 / 1.0 | 10 / 10 |
    Simple Benchmark        | 5.142354x   | 1 / 1.0 | 5 / 5   |
    Comprehensive Benchmark | 131.328121x | 1 / 1.0 | 17 / 17 |



There was also a leaderboard for this project, ranking the submissions by how much they beat the benchmark. **My submission was ranked 5th best in the course!**

I want to wrap up by saying this is my favorite project I've ever done in the 61 series of classes!
It's been an absolute blast, used so many cool pieces of material, and employed all sorts of handy algorithmic tricks.
I did the brunt of this project in a two day sprint, and can honestly say that I enjoyed every minute of it.


To summarize how I achieved the speedups I did:

  I used Open MP on every for loop. This is a rather practical, if naïve approach to multithreading.
  Still, simply adding "#pragma omp parallel for" in front of loops proved to be effective, without resulting in data races.

  I used loop unrolling wherever possible. The benefits of loop unrolling are admittedly quite small, but it is rather trivial to implement.

  For exponentiation I employed an algorithm called Exponentiation By Squaring, covered in CS70. It allowed me to speed up my power function by 700x.

  For multiplication (and by extension, power) I used cache blocking, which allowed me to exploit spatial locality. This sped up my multiplication algorithm by 30x.

  For absolute value I'm rather proud of the simple bit switching trick I used, where I AND the number I want the absolute value of with the NOT of the binary representation of -0.0,
  which resembles 0b100...000. After NOT, this becomes 0b0111...111, and effectively just turns off the most significant bit.
  For floats, this is the same effect as taking the absolute value!

  Finally, I secured my biggest speedup by using AVX intrinsics. These functions allow one to perform rapid vector operations.
  Employing these was tricky, but after figuring out the process of breaking down the task I had into basic steps, it was straightforward to implement.
  I used this method in all but my power function, (it did not need the speedup for me to achieve full points), and I think this is what helped put me on the leaderboard.

Thanks for reading!
