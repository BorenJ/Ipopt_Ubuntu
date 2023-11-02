# Ipopt_Ubuntu Install
This project is intended to install and use NLP Ipopt in the ubuntu ros environment. The programming language is C++ and VSCode. After this, you can run the official example in /Ipopt/examples/hs071_cpp.
- **Getting System Packages (Compilers,...)**
`sudo apt-get install gcc g++ gfortran git patch wget pkg-config liblapack-dev libmetis-dev` 

- **DownLoad BLAS and LAPACK**
`sudo apt-get install libblas-dev liblapack-dev`

- **DownLoad HSL**
1. Go to http://hsl.rl.ac.uk/ipopt.
2. Register and login, choose to order '**HSL Academic Licence**', after one working day, you will receive a download link in your email.
3. Download 'coinhsl-2023.05.26.zip' (only 1MB). And extract. Change the folder name as `coinhsl`. Now in my PC, the path is `boren@boren:~/Downloads$`.
4. ```
	mkdir ThirdParty
	mv coinhsl ThirdParty/
	mkdir ThirdParty/coinhsl/build
	sudo apt-get install meson
	cd ThirdParty/coinhsl/build
	 ``` 
	 **Please pay attention!** Please change the path`/home/boren` to your own account name for the following code.
	 ```
	meson setup --buildtype=release --prefix=/home/boren/Downloads/ThirdParty/ ..  
	ninja
	ninja install
	cd ~/Downloads/ThirdParty
	ls #  After this, ThirdParty should has other two folders as 'include' and 'lib'.  
	 ``` 
5. Move HSL files (header files .h and library .so) to `usr/local`, one is include_directories as `usr/local/include`, another is target_link_directories `usr/local/lib`. Please pay attention to these two paths where our Ipopt will also be installed.
	```
	 sudo mv lib/x86_64-linux-gnu/libcoinhsl.so /usr/local/lib
	 cd ~/Downloads/ThirdParty
	 sudo mv include hsl  # change hsl header folder name to 'hsl'
	 sudo mv hsl /usr/local/include  # move hsl header files to /usr/local/include/
	 cd /usr/local/lib 
	 sudo mv libcoinhsl.so libhsl.so  # the Ipopt find hsl library named 'libhsl.so', so change the name. 
	 ```
	Now in the 'include_directories' `/usr/local/include`, you will see folder named `hsl` containing hsl header files, and in  'target_link_directories' `/usr/local/lib`, you will see `libhsl.so` file. We will costumize CMakeLists.txt according these two paths later.

- **Install Ipopt**
	```
	cd ~/Downloads
	git clone https://github.com/coin-or/Ipopt.git
	cd Ipopt
	mkdir build
	./configure
	make
	make test
	sudo make install
	```
	Now in 'include_directories' `/usr/local/include` you can see `coin-or` folder, which contains the header files of Ipopt, and 'target_link_directories' `/usr/local/lib`, you can see Ipopt library files. 
	
 # Run Ipopt Example
 - **Build ros working space**.  In the path you like, in my case is home path, build ros work space (Please refer to  [Build ROS WorkSpace](https://github.com/BorenJ/ChernoCPPSeriesPractice/tree/main), from section 1 to 4. 
 - **Copy examples**. Copy examples in  `/home/boren/Ipopt/examples/` ( `hs071_main.cpp`,`hs071_nlp.cpp`,`hs071_nlp.hpp` ), to ros packages src, in my case is `opt_ws/src/opt_pack/src`, where 'op_ws' is ros working space and 'opt_pack' is ros package. 
 - **CMakeLists.txt**. Add following codes to CMakeList.txt
	```
	include_directories(
	${catkin_INCLUDE_DIRS}
	"/usr/local/include/coin-or"
	)
	
	add_executable(OPT src/hs071_main.cpp src/hs071_nlp.cpp)
	
	target_link_libraries(OPT
	${catkin_LIBRARIES}
	"/usr/local/lib/libipopt.so"
	)
	 ```
	 In VSCode terminal,
	 ```
	source ./devel/setup.bash
	echo $LD_LIBRARY_PATH
	export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
	 ```
	 
 - Finally, `Ctrl + Shift + B` compile the package, and you can run `rosrun opt_pack OPT` VSCode terminal and see the results.
 - In VSCode, you may see red wavy underline for `#include  "IpIpoptApplication.hpp"` .etc, in this case, simply write as `#include  "coin-or/IpIpoptApplication.hpp"` will be fine.
