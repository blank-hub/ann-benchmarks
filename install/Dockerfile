FROM ubuntu:18.04

RUN apt-get update
RUN apt-get install -y python3-numpy python3-scipy python3-pip build-essential git
RUN pip3 install -U pip

WORKDIR /home/app
RUN git clone -b local https://github.com/blank-hub/ann-benchmarks
#COPY requirements.txt run_algorithm.py ./
RUN cd ann-benchmarks && pip3 install -r requirements.txt




WORKDIR /home/app
RUN git clone https://github.com/microsoft/SPTAG
RUN apt-get update && apt-get -y install wget build-essential libtbb-dev software-properties-common swig

# cmake >= 3.12 is required
RUN wget "https://github.com/Kitware/CMake/releases/download/v3.14.4/cmake-3.14.4-Linux-x86_64.tar.gz" -q -O - \
        | tar -xz --strip-components=1 -C /usr/local

# specific version of boost
RUN wget "https://boostorg.jfrog.io/artifactory/main/release/1.67.0/source/boost_1_67_0.tar.gz" -q -O - \
        | tar -xz && \
        cd boost_1_67_0 && \
        ./bootstrap.sh && \
        ./b2 install && \
        # update ld cache so it finds boost in /usr/local/lib
        ldconfig && \
        cd .. && rm -rf boost_1_67_0

# SPTAG defaults to Python 2 if it's found on the system, so as a hack, we remove it. See https://github.com/microsoft/SPTAG/blob/master/Wrappers/CMakeLists.txt
RUN apt-get -y remove libpython2.7

# Compile
RUN cd SPTAG && mkdir build && cd build && cmake .. && make && cd ..

# so python can find the SPTAG module
ENV PYTHONPATH=/home/app/SPTAG/Release
RUN python3 -c 'import SPTAG'

WORKDIR /home/app/ann-benchmarks
ENTRYPOINT ["python3", "-u", "run_algorithm.py"]
