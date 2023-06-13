---
created: [" 14-06-2023 00:49"]
aliases: []
tags:
- article/
---

# Logs in container
![[Pasted image 20230614010642.png]]
## Logs in container in stderr and stdout

`docker logs <container>` - get logs from `contaner`

`-f` - "follow" - attach logs to output (don't exit)
`-t` - add time to logs


- logs can be seen even in closed containers

## Transform stderr to stdout and parse only ERROR logs:

`docker logs <container> 2>&1 | grep ERROR`


