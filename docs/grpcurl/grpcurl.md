### Call using Proto File
---
``` bash
$ grpcurl -v -plaintext -proto D:\Protos\greet.proto -import-path D:\Protos -d '{"name": "World"}' localhost:5000 greet.Greeter/SayHello
```

- `-v` debugging
- `-proto` proto file path
- `import-path` proto folder path
- `-d` grpc method request in json formate