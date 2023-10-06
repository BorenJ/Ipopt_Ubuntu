# Ipopt_Ubuntu Install
This project is intended to install and use NLP Ipopt in the ubuntu ros environment. The programming language is C++ and VSCode.
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
	meson setup --buildtype=release --prefix=/home/boren/Downloads/ThirdParty/ ..  # Please change the path'/home/boren' to your own account name.
	ninja
	ninja install
	cd ~/Downloads/ThirdParty
	ls #  After this, ThirdParty should has other two folders as 'include' and 'lib'.  
	 ``` 
5. Move HSL files (header files .h and library .so) to `usr/local`, one is include_directories as `usr/local/include`, another is target_link_directories `usr/local/lib`. Please pay attention to these two paths where our Ipopt will also be installed.
	```
	 mv lib/x86_64-linux-gnu/libcoinhsl.so /usr/local/lib
	 cd ~/Downloads/ThirdParty
	 mv include hsl  # change hsl header folder name to 'hsl'
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
