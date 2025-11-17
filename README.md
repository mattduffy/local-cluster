# local-cluster
TuringPi mini cluster setup and SBC module configuration steps and notes.

### llama.cpp
Dependencies:
* libcurl4-openssl-dev (include libcurl4-gnutls-dev for good measure)
* ccache for faster recompiling of llama.cpp
* ~libblis-dev~ build blis from source https://github.com/flame/blis

#### building blis from source
```bash
git clone https://github.com/flame/blis
cd blis
./configure --libdir=/usr/lib --includedir=/usr/include --enable-cblas -t openmp,pthreads auto
make -j 8
make check
sudo make install
```

#### building llama.cpp from source (with BLIS)
```bash
export BLIS_LIBDIR=/usr/lib
export BLIS_INCDIR=/usr/include/blis
cmake -B build -DGGML_BLAS=ON -DGGML_BLAS_VENDOR=FLAME -DBLAS_INCLUDE_DIRS=/usr/include/blis -DGGML_CCACHE=OFF
cmake --build build --config Release -j 8

# before running llama executable
export GOMP_CPU_AFFINITY="0-19"
export BLIS_NUM_THREADS=14
```
keeps failing during BLAS build phase.  

#### building regular llama.cpp from source (without BLIS)
```bash
cmake -B build 
cmake --build build --config Release -j 8

# before running llama executable
export GOMP_CPU_AFFINITY="0-19"
export BLIS_NUM_THREADS=14
```
