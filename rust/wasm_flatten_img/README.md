# Wasm version image flattening and calling tensorflow for face recognition

## Setup the `ssvmup`

```bash
$ npm install -g ssvmup
```

## Build

### Build wasm source

```bash
$ ssvmup build
```

The result wasm file will be at `pkg/wasm_flatten_img_bg.wasm`.

### Build ssvm

Get [ssvm](https://github.com/second-state/SSVM).

```bash
$ docker pull secondstate/ssvm
$ docker run -it --rm \
    -v <path/to/your/ssvm/source/folder>:/root/ssvm \
    secondstate/ssvm:latest
(docker)$ cd /root/ssvm
(docker)$ mkdir -p build && cd build
(docker)$ cmake -DCMAKE_BUILD_TYPE=Release .. && make -j
```

### Build face_detect binary

See [face_detect](https://github.com/second-state/wasm-learning/tree/master/rust/face_detect).

```bash
# Copy face_condition binary to /usr/bin/
(docker)$ cp face_detect /usr/bin/
# Install the tensorflow library
(docker)$ wget https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-gpu-linux-x86_64-1.15.0.tar.gz
(docker)$ tar -C /usr/local -xzf libtensorflow-gpu-linux-x86_64-1.15.0.tar.gz
(docker)$ ldconfig
```

## Run

Interpreter mode:

```bash
(docker)$ cd /root/ssvm/build/tools/ssvm
# Copy input image and wasm file to /root/ssvm/build/tools/ssvm/
(docker)$ ./ssvm --dir .:. wasm_flatten_img_bg.wasm input_path.jpg output_path.jpg
```

AOT mode:

```bash
(docker)$ cd /root/ssvm/build/tools/ssvm-aot
# Copy input image and wasm file to /root/ssvm/build/tools/ssvm-aot/
(docker)$ ./ssvmc wasm_flatten_img_bg.wasm wasm_flatten_img_bg.wasm.so
(docker)$ ./ssvmr --dir .:. wasm_flatten_img_bg.wasm.so input_path.jpg output_path.jpg
```
