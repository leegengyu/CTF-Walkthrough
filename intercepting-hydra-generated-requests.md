# Intercepting Hydra-generated Requests via Burp Suite Proxy

* Assuming that there is a proxy listener set up in Burp Suite with the interface `127.0.0.1:8080` that is running, run the following commands in Terminal:
1. `export HYDRA_PROXY_HTTP="http://127.0.0.1:8080/"` 
2. `printenv | grep HYDRA` (verify that the environment variable is successfully set)
* Initially, what I learnt was 2 separate commands (for command 1): `HYDRA_PROXY_HTTP="http://127.0.0.1:8080/"` to define a variable first, before `export HYDRA_PROXY_HTTP`, which is to export the variable as an environment variable. However, I learnt subsequently that I could combine the two commands into one for efficiency.
* Run a `hydra` command to brute-force something, and we observe the following line printed as part of the output (found after the start date/time): `[INFO] Using HTTP Proxy: http://127.0.0.1:8080/`.
* This shows that the above has been set up correctly, and hydra's requests will now be seen/intercepted in Burp Suite.

## To-Note ##
* We cannot define any random variable with the proxy listener's address - it has to be `HYDRA_PROXY_HTTP` for HTTP services only, and `HYDRA_PROXY` for all other services (see [Hydra's GitHub page](https://github.com/vanhauser-thc/thc-hydra) for the official word about it).
* If the wrong environment variable was set, run `unset <variable name>` to remove it as an environment variable.
* If both of the listed hydra environment variables are set, an error message will be seen and hydra will stop running: `[ERROR] Found HYDRA_PROXY_HTTP *and* HYDRA_PROXY environment variables - you can use only ONE for the service http-head/http-get!`.
* I had accidentally defined the contents of `HYDRA_PROXY_HTTP` for `HYDRA_PROXY` instead, and this error message was observed: `[ERROR] proxy defined but not valid, exiting`. Hydra will also stop running in this case.

## To-Find-Out ##
* I noticed that there were many **unnecessary GET requests made** as part of the brute-forcing process. It appeared that for every 1 POST request made, there was 1 GET request made to the same page. Trying to find out why this is happening. This issue was also observed by g0tmi1k in his write-up [here](https://blog.g0tmi1k.com/dvwa/login/). If I'm not wrong, he did not state why these unnecessary GET requests were being made, save for the fact that there is thus a need to test before attacking.