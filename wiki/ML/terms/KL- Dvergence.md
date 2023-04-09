---
created: [" 09-04-2023 11:35"]
aliases: []
tags:
- terms/
---

# KL- Dvergence

> **Kullback–Leibler divergence**

## definition
-  ![{\displaystyle D_{\text{KL}}(P\parallel Q)}](https://wikimedia.org/api/rest_v1/media/math/render/svg/039fa82bd08654b4faa2b32ded70c0160554fa07)  - measure of how probability distribution P os different from distribution Q
-  measure of statistical distance
-  not a metric

for continuous distribution:
![{\displaystyle D_{\text{KL}}(P\parallel Q)=\int _{-\infty }^{\infty }p(x)\log \left({\frac {p(x)}{q(x)}}\right)\,dx}](https://wikimedia.org/api/rest_v1/media/math/render/svg/756dd25036c5da76a59e58a001f3196e059f537d)

- High where $p(x) > q(x)$
- Not interested in areas where $p(x)$ is 0 or close to 0
## useful links
- [[DDPM]]