---
created: [" 14-06-2023 00:27"]
aliases: []
tags:
- article/
---

# Environmental variables

## In [[Dockerfile]] - non-secret variables
`ENV <VAR>=...` - environmental variable `VAR`

## Get secret variables while starting docker:
```{bash}
docker run -it --rm -e ENV_VAR=$SECRET_TOKEN ubuntu
```

