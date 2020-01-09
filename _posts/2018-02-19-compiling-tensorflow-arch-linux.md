---
layout: single
title: "Compiling TensorFlow on Arch Linux"
permalink: compiling-tensorflow-arch-linux
tags: [TensorFlow, Arch Linux, deep learning]
---

> Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA

The above notification keep popping up whenever you use TensorFlow to remind you that your models could be training faster if you used binaries compiled with the right configuration.

When TensorFlow first came out, it was available as a package, it became available to Arch Linux users as a [package in the Arch User Repository](https://aur.archlinux.org/packages/python-tensorflow-git/), meaning that it was compiled on your local system.

Although this process is time consuming, the fact that the binaries are compiled on your local system, means that they can make full use of the instructions of your processor.

Around the time that TensorFlow 1.0 came out, it became available as pre-compiled binaries in the [community repository](https://www.archlinux.org/packages/community/x86_64/python-tensorflow/), the advantage of this is that installation is a matter of seconds.

However, I keep getting the above notification on TensorFlow not using the instructions that my processor supports (I have an Asus [UX370UA](https://www.asus.com/us/2-in-1-PCs/ASUS-ZenBook-Flip-S-UX370UA/)), which came out last year.

Since computing power is a bottleneck machine learning, it makes little sense not to use optimised software, I therefore went to the [Installing TensorFlow from Sources](https://www.tensorflow.org/install/install_sources) page, which describes the process.

Since you will be installing TensorFlow using [pip](https://pip.pypa.io/en/stable/), make sure that you first remove the current installation of TensorFlow.

```
# pacman -R tensorflow
```

Next we need to make sure that [bazel](https://bazel.build/) is installed, so that we can build our binaries.

```
# pacman -S bazel
```

The next step is to clone the TensorFlow repository.

```
$ git clone https://github.com/tensorflow/tensorflow.git
$ cd tensorflow
```

Next we run the bash script (note if you have a NVidia GPU you need to take the extra steps for CUDA before this):

```
$ ./configure
```

This will ask a series of questions on your system, the defaults should work fine.


At the time of writing (February 2018), the latest version of gcc in the Arch Linux Core repository is 7.3.1, the installation page has a note on using gcc versions greater than 4, some extra instructions need to be added. The full build command looks like this:

```
$ bazel build --config=opt --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" //tensorflow/tools/pip_package:build_pip_package

```

Building will take some time (up to several hours). After this you need to create the pip package.

```
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

The final step is simply to install the pip package (note that the actual filename of the wheel will be different, but it should be similar).

```
# pip install /tmp/tensorflow_pkg/tensorflow-1.6.0rc1-cp36-cp36m-linux_x86_64.whl 
```

We can now test our optimised TensorFlow installation, first invoke python:

```
$ python
```

In python run:

```
# Python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

If all is well, this should print out (without any notices such as the one at the top):

```
Hello, TensorFlow!
```

Finally (back in bash), we need to run:

```
$ bazel clean
```

So that, if any new features are released, we can simply `git pull` and start the building over again (from configure).

