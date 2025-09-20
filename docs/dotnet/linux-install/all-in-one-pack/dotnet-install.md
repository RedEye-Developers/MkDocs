### Step 1 :
---
#### Download Dotnet All in One Shell File

**Dotnet Net All in One : [Link](https://dotnet.microsoft.com/en-us/download/dotnet/scripts "click to open in browser")**

``` bash
$ wget https://builds.dotnet.microsoft.com/dotnet/scripts/v1/dotnet-install.sh
```

### Step 2 :
---
#### Give Permission To Execute File

``` bash
$ chmod +x ./dotnet-install.sh
```

### Step 3 :
---
#### Install Dotnet Sdk and Runtime

!!! warning "WARNING" 

    Must set the `--verison 9.0.0`, if you not set `--version` or `--version latest` it will install the old version dotnet skd and runtime.

``` bash
$ ./dotnet-install.sh  --architecture amd64 --channel LTS --os linux --version 9.0.303
```

### Step 4 :
---
#### Set Dotnet Evn Variable Path

```bash
$ export PATH=$PATH:/root/.dotnet
```