# The Simulated Annealing algorithm applied to the problem of finding the "minimal energy" of an image

* The image energy is calculated as the sum of the <strong>absolute differences between all horizontally and vertically adjacent pixels in all 3 color channels</strong>.
  
* Testing can be done starting with  randomly generated <strong>32x32</strong> images.
  
* When testing neighboring solutions, in each iteration we randomly select one pixel and replace it with <strong>a neighboring pixel located to the right or below the selected pixel</strong>.
  
* If the new image energy is higher (the energy is higher), we calculate <strong>the probability of accepting the change</strong> as:
$$P = 2^{-\frac{dE}{T_t}} $$ where dE is the change in energy of the system and T<sub>t</sub> is the current temperature.
<strong>The system is cooled linearly</strong>:
$$T_t=T_s⋅(1−\frac{i}{i_{max}})$$ where Ts is the initial temperature and the number of current iterations, and i<sub>max</sub> is the total number of iterations.

## Tasks
* <strong>Copy the image matrix</strong> from global to shared memory using as many threads per block as possible.
  
* <strong>Calculate the energy of the initial matrix</strong> using as many threads per block as possible. Put the result in shared memory.
  
* <strong>Implement the simulated annealing process</strong> by using 12 threads along the x dimension of the block and testing multiple solutions in an iteration using the y dimension of the block and selecting the best one.

* <strong>Return the image, final energy and number of swaps</strong> after the simulation of annealing.
  
## Implementation
The implementation is in <strong>PyCUDA</strong> environment. Two levels of parallelism are implemented:

* Threads along the x dimension of the block are used to calculate the image energy change during swapping. The energy is computed based on <strong>at most a 3x4 or 4x3 submatrix around the elements that are being swapped</strong> (depending on whether it is swapping with its neighbor to the right or below). Individual pixel energies (before modification) are not stored, but recalculated.

* Each of the 12 threads in the block calculates the energy of one pixel (the absolute difference between the pixel on the right and the pixel below) before and after the swap. The result is being written to the shared memory. The total energy change is calculated using a single thread. The y threads of the block are used to calculate several alternative swaps, from which the best one will be chosen. The swap is being done using only one thread in a block.
