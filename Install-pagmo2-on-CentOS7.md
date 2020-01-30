# Install pagmo2 on CentOS7

## CMake

```bash
$ sudo yum install -y epel-release
$ sudo yum install -y cmake3
Installed:
  cmake3.x86_64 0:3.14.6-2.el7
$ sudo cmake3 --version
cmake3 version 3.14.6
```

## Boost C++ Library

[boost_1_71_0.tar.bz2](https://dl.bintray.com/boostorg/release/1.71.0/source/boost_1_71_0.tar.bz2)

```bash
$ cd
$ sudo yum install -y bzip2
$ wget https://dl.bintray.com/boostorg/release/1.71.0/source/boost_1_71_0.tar.bz2
$ tar --bzip2 -xf boost_1_71_0.tar.bz2
$ cd boost_1_71_0
$ sudo ./bootstrap.sh
$ sudo ./b2 install
$ cd
```

## Intel TBB

[CMake for Intel TBB](https://github.com/os-popt/tbb), forked from [CMake for Intel TBB](https://github.com/wjakob/tbb)

```bash
$ cd
$ git clone https://github.com/os-popt/tbb.git
$ cd tbb/build
$ cmake3 ..
$ make -j
$ sudo make install
$ cd
```

## eigen3

```bash
$ sudo yum install -y eigen3-devel
Installed:
  eigen3-devel.noarch 0:3.3.4-7.el7
```

## pagmo2

```bash
$ cd
$ wget https://github.com/esa/pagmo2/archive/v2.13.0.tar.gz
$ tar -zvxf v2.13.0.tar.gz
$ cd pagmo2-2.13.0/
$ mkdir build
$ cd build
$ cmake3 ../ -DPAGMO_BUILD_TESTS=ON -DPAGMO_WITH_EIGEN3=ON
$ cmake3 --build .
$ sudo cmake3 --build . --target install
$ cmake3 --build . --target test
$ cd
$ ls /usr/local/lib | grep pagmo
libpagmo.so
libpagmo.so.3
libpagmo.so.3.0

$ vi ~/.bash_profile
# Add path for pagmo2
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
export LD_LIBRARY_PATH
$ . .bash_profile
```

### Run and Test

```bash
$ cd
$ vi getting_started.cpp
```

```c
/*
 * https://esa.github.io/pagmo2/quickstart.html
 */
#include <iostream>
#include <pagmo/algorithm.hpp>
#include <pagmo/algorithms/sade.hpp>
#include <pagmo/archipelago.hpp>
#include <pagmo/problem.hpp>
#include <pagmo/problems/schwefel.hpp>
using namespace pagmo;
int main()
{
// 1 - Instantiate a pagmo problem constructing it from a UDP
// (user defined problem).
problem prob{schwefel(30)};
// 2 - Instantiate a pagmo algorithm
algorithm algo{sade(100)};
// 3 - Instantiate an archipelago with 16 islands having each 20 individuals
archipelago archi{16u, algo, prob, 20u};
// 4 - Run the evolution in parallel on the 16 separate islands 10 times.
archi.evolve(10);
// 5 - Wait for the evolutions to be finished
archi.wait_check();
// 6 - Print the fitness of the best solution in each island
for (const auto &isl : archi) {
std::cout << isl.get_population().champion_f()[0] << '\n';
}
}
```

```bash
$ g++ -O2 -DNDEBUG -std=c++11 getting_started.cpp -pthread -lpagmo -lboost_serialization -ltbb
$ ./a.out
```
