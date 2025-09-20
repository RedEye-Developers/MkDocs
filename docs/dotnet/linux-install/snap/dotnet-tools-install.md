### 1. Install the Aspire CLI .NET tool :
---
``` bash
$ dotnet tool install -g Aspire.Cli --prerelease
```

### 2. Tell bash where the aspire command is installed :
---
``` bash
$ echo 'export PATH="$PATH:/home/$USER/.dotnet/tools"' >> ~/.bashrc
```

### 4. Tell the aspire CLI binary where to find the .NET runtime :
---
``` bash
$ echo 'export DOTNET_ROOT="/var/snap/dotnet/common/dotnet"' >> ~/.bashrc
```

### 5. Load the values we just added with step 4 and 5 :
---
``` bash
$ source ~/.bashrc
$ Verify that the aspire CLI runs without any issue:
$ aspire --version
```