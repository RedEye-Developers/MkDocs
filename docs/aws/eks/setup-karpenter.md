## Remember-Docs

### Subnet Settings
#### Auto-Assign public IP
---

- **Public Subnets** Must : ***Enable auto-assign public IPv4 address : true***

- **Private Subnets** Canbe : ***Enable auto-assign public IPv4 address : false or true***

#### Tags

1. Set Both Public and Private Subnets : ***karpenter.sh/discovery : cluster-name***

2. Set For Public Subnet Only : ***kubernetes.io/role/elb : 1***

3. Set For Private Subnet Only : ***kubernetes.io/role/internal-elb : 1***