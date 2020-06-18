### [A2] Task Mapping on Soft Heterogeneous Systems 
Apan Qasem [\<apan@txstate.edu\>](apan@txstate.edu)

#### Demo: Task Mapping on an DVFS-enabled Heterogeneous System

##### Objective

Demonstrate the performance and energy impact of operational frequency on heterogeneous multicore systems. 


##### Software and Tools

The following Linux tools are used in this demo.

  * `cpufrequtils`
  * `cpupower`
  * `perf`
  * `energy`
  * `taskset`

The demo also includes a simple C++/OpenMP code that performance matrix-vector multiplication in
parallel. 

##### Environment Set-up

These steps should be carried out prior to class time.

###### Simulate a multicore heterogeneous platform. 

1. Install necessary packages and their dependencies

Install `cpufrequtils`
  
     sudo apt install cpufrequtils

Install `perf`, `taskset` and `cpupower` if they are not alreay installed. These tools are available in the common tools package

     sudo apt install linux-tools-common
     sudo apt install linux-tools-4.15.0-47-generic
     

2. Lower Frequencies for a subset of cores

We simulate a less powerful (i.e., _little_) core by lowering its frequency to the lowest allowed
value. In this example we select cores 0 and 1 to be the _little_ cores. To lower the frequencies for
cores 0 and 1

     cpupower -c 0 frequency-set -d 1800000
     cpupower -c 0 frequency-set -u 1800000
     cpupower -c 1 frequency-set -d 1800000
     cpupower -c 1 frequency-set -u 1800000


The above will set up the multicore system with two _big_ cores (cores 2 and 3) and two _little_
cores (cores 0 and 1). Other more complex configurations can be easily set up if the instructor
chooses to do a more involved (e.g., in a CS2 course rather CS1)


##### In-class Demonstration

The main steps for the in-class demo are outlined below

#######  Discuss heterogeneous system.

Log into system that has been set up to simulate a heterogeneous system and review it's attributes. 

    cat /proc/cpuinfo
    cpufreq-info
	
#######  Review matrix-multiply code

Pull up the matrix-vector source code in an editor and do a walk-through.

  - discuss command-line arguments 
  - discuss basics of an OpenMP directive


####### Build the code on the command-line.

    g++ -o matvec -fopenmp -O3 matvec.c      

Run and time the code 

    time ./matvec 10000 4

####### Discuss mapping of threads to processors 

   Introduce the taskset utility and discuss how it can be used to map threads to processing cores.

    taskset -c 0 ./matvec  // run program on core 0
    taskset -c 0-3 ./matvec // run program on all cores (0 through 3)

####### Run code on _little_ cores

   Run the code on the cores set up as little cores and measure execution time.

    time taskset -c 0,1 ./matvec

   Re-run the code and measure detailed performance metrics with perf

    perf stat taskset -c 0,1 ./matvec

   Re-run the code and measure power and energy  

    likwid-perfctr -c 0,1 -g ENERGY taskset -c 0,1 ./matvec

####### Run code on _big_ cores

   Run the code on the cores set up as little cores and measure execution time.

    time taskset -c 2,3 ./matvec 

   Re-run the code and measure power and energy  

    likwid-perfctr -c 0,1 -g ENERGY taskset -c 0,1 ./matvec


####### Discuss the implications of the results

   - little cores will consume less power than big cores
   - little cores will have lower performance than big cores
   - threads must be mapped to cores based on the characteristic of the application and the target
      objective



   







