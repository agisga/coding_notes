- Start an SSH tunnel from the local machine:
  ```
  local_user@local_host$ ssh -N -L localhost:8888:localhost:8889 remote_user@remote_host
  local_user@local_host$ ssh -N -f -L localhost:8888:localhost:8889 remote_user@remote_host
  ```
  The first option -N tells SSH that no remote commands will be executed, and is useful for port forwarding. The second option -f has the effect that SSH will go to background, so the local tunnel-enabling terminal remains usable. The last option -L lists the port forwarding configuration (remote port 8889 to local port 8888). From: <https://coderwall.com/p/ohk6cg/remote-access-to-ipython-notebooks-via-ssh>
