# logseq

## Self-hosting on my own machine, and connecting from other computers to use the running instance

1. Follow deployment instructions given at <https://github.com/logseq/logseq#set-up-development-environment>.

2. In a *Chrome*-based browser on another machine run:

```
ssh -L 3001:localhost:3001 -L 9630:localhost:9630 -L 8701:localhost:8701 my_host_machine
```
