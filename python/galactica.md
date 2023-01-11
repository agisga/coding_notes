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

Another example:

```
In [36]: r = model.generate("To demonstrate safety and effectiveness of the AI-based denoising software, the primary endpoint of the evaluation study", new_doc=False, max_new_tokens=500)



In [37]: print(r)
To demonstrate safety and effectiveness of the AI-based denoising software, the primary endpoint of the evaluation study was the change in the mean signal intensity (ΔSI) of the lesion on the T2-weighted images. The secondary endpoint was the change in the mean signal intensity (ΔSI) of the lesion on the T1-weighted images.



# 2.4. Statistical Analysis



The data were analyzed using the SPSS software (version 25.0; IBM Corp., Armonk, NY, USA). The normality of the data was assessed using the Shapiro–Wilk test. The data were presented as the mean ± standard deviation (SD) or median (interquartile range). The paired t-test or Wilcoxon signed-rank test was used to compare the ΔSI of the lesion on the T2-weighted images and T1-weighted images between the two groups. The Mann–Whitney U test was used to compare the ΔSI of the lesion on the T2-weighted images and T1-weighted images between the two groups. The chi-square test was used to compare the number of patients with a ΔSI of the lesion on the T2-weighted images and T1-weighted images of >10% between the two groups. A p-value of <0.05 was considered statistically significant.



# 3. Results



# 3.1. Patient Characteristics



A total of 100 patients were enrolled in this study. The patient characteristics are summarized in Table 1. The mean age of the patients was 60.3 ± 11.3 years, and 52 patients (52%) were men. The mean tumor size was 2.9 ± 1.4 cm. The mean ΔSI of the lesion on the T2-weighted images was 10.3 ± 10.1%. The mean ΔSI of the lesion on the T1-weighted images was 10.1 ± 10.1%.



# 3.2. Comparison of the ΔSI of the Lesion on the T2-Weighted Images and T1-Weighted Images between the Two Groups



The ΔSI of the lesion on the T2-weighted images was significantly higher in the AI-based denoising group than in the conventional denoising group (12.3 ± 10.3% vs. 8.9 ± 9
```
