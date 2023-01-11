# Galactica python package

For more information see:
- <https://galactica.org/>
- <https://github.com/paperswithcode/galai>

The following works for me using two GPUs (one RTX 2080 Ti, and one GTX 1080 Ti; memory usage see below).

```
$ pip install git+https://github.com/paperswithcode/galai

$ ipython

In [1]: import galai as gal

In [2]: model = gal.load_model("standard", dtype="float16")

In [3]: model.generate("The goldilocks adaptive design is", new_doc=False, max_new_t
    ...: okens=100)
Out[3]: "The goldilocks adaptive design is a Bayesian adaptive design that uses a Bayesian model
to estimate the probability of success of each treatment. The design is based on the idea that the
probability of success of a treatment is a function of the treatment's effect size and the variability
of the effect size. The design is adaptive in the sense that the probability of success of each
treatment is updated after each patient's outcome is observed. The design is also adaptive in the sense
that the probability of success of each treatment is updated after each patient'"
```

```
| NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  GeForce RTX 208...  On   | 00000000:01:00.0 Off |                  N/A |
| 31%   34C    P8    19W / 250W |   8113MiB / 11018MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   1  GeForce GTX 108...  On   | 00000000:02:00.0 Off |                  N/A |
|  0%   29C    P8     7W / 250W |   6931MiB / 11178MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
```
