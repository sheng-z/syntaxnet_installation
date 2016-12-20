### Environment
System: Ubuntu 16.04 64bit 

bazel: 0.3.1 

swig: 3.0.10/3.0.8 

Python: 2.7.12 

Anaconda: 4.2.0

Cuda: 7.5 

Cudnn: 5.1.3 

GPU: Titan X 

### Syntaxnet SHA1
Installation has been tested on `c5bb05430dcc6a2c377ffed35b00a18b402fcbc3`

### Installation
1. [Anaconda](https://www.continuum.io/downloads)
2. Follow [installation instructions](https://github.com/tensorflow/models/tree/master/syntaxnet#installation) on Syntaxnet github page.
    - [bazel](https://bazel.build/versions/master/docs/install.html#install-with-installer-ubuntu)
    - swig([local installation instructions](http://www.linuxfromscratch.org/blfs/view/7.6/general/swig.html))
    - protocol buffers: `pip install -U protobuf==3.0.0b2`
    - asciitree: `pip install asciitree`
    - numpy: `pip install numpy`
    - mock: `pip install mock`
3. `git clone --recursive https://github.com/tensorflow/models.git`
4. `cd models/syntaxnet/tensorflow`
5. Modifications for GPU support ([issues/248](https://github.com/tensorflow/models/issues/248))
    - Make sure you have the following environment variables set: `CUDA_HOME="[path_to_cuda_top_directory]" LD_LIBRARY_PATH="[path_to_cuda_lib64_directory] :$LD_LIBRARY_PATH" PATH="[path_to_cuda_bin_directory]:$PATH"`
    - Add the line `build --config=cuda` to `tools/bazel.rc`
    - set `crosstool_top` in `tools/bazel.rc` to `cuda --crosstool_top=@org_tensorflow//third_party/gpus/crosstool`
    - Add the line `cxx_builtin_include_directory: "[your cuda/targets/x86_64-linux/include]"`, and `cxx_builtin_include_directory: "[your cudnn/include]"` to `tensorflow/third_party/gpus/crosstool/CROSSTOOL` (with the cuda part pointing to your Cuda installation)
    - Force Tensorflow to use Cuda by changing the `//conditions:default` part in `syntaxnet/syntaxnet.bzl` from `if_false` to `if_true`
    - Do the same thing for `tensorflow/third_party/gpus/cuda/build_defs.bzl`
6. Other modifications:
    - Add the line `cxx_builtin_include_directory: "[your Anaconda/include]"`, and `cxx_builtin_include_directory: "[your Anaconda/include/python2.7]"` to `tensorflow/third_party/gpus/crosstool/CROSSTOOL` (with the cuda part pointing to your Cuda installation)
    - add the line `use_default_shell_env=True,` to `tensorflow.bzl` (e.g., for v0.10, add the line to [here](https://github.com/tensorflow/tensorflow/blob/r0.10/tensorflow/tensorflow.bzl#L483))

7. `./configure`

8. Build Syntaxnet:
`bazel test -c opt --config=cuda --define using_cuda_nvcc=true --define using_gcudacc=true syntaxnet/... util/utf8/... --genrule_strategy=standalone --spawn_strategy=standalone`

9. If you met errors, please clean before rebuilding:
`bazel clean --expunge_async`
