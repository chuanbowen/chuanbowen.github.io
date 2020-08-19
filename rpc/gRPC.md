## gRPC
定义一个服务，可被远程调用  
默认使用pb作为接口定义语言，描述服务接口以及协议  
支持多种开发语言  
使用HTTP2.0协议设计开发

## Simple RPC
### Golang 环境 & Demo
1. refer: https://grpc.io/docs/languages/python/quickstart/
2. 安装编译, Golang1.14
   ``` bash
   export GO111MODULE=on 
   go get github.com/golang/protobuf/protoc-gen-go
   protoc -I . --go_out=plugins=grpc:. ./xxx.proto
   ```
3. Demo
    ``` Golang
    package main
    import (
        "context"
        "fmt"
        "log"
        "time"
        lrpc "seckey/rpc"
        "google.golang.org/grpc"
        "google.golang.org/grpc/keepalive"
    )
    func main() {
        var kacp = keepalive.ClientParameters{
            Time:                10 * time.Second,
            Timeout:             20 * time.Second,
            PermitWithoutStream: false,
        }
        conn, err := grpc.Dial("127.0.0.1:8081", grpc.WithInsecure(), grpc.WithKeepaliveParams(kacp))
        if err != nil {
            log.Fatal(err)
        }
        defer conn.Close()
        er := &lrpc.EncryptRequest{
            Token: "test",
            Data:  []string{"test", "test"},
        }
        client := lrpc.NewCryptClient(conn)
        reply, err := client.DoEncrypt(context.Background(), er)
        if err != nil {
            log.Fatal(err)
        }
        fmt.Println(reply.String())
    }
    ```

### Python 环境 & Demo
1. refer： https://grpc.io/docs/languages/python/quickstart/
2. 安装编译
   ``` bash
   python -m pip install grpcio-tools
   python -m grpc_tools.protoc  --python_out=. --grpc_python_out=. -I. xxx.proto
   ```
   生成xxx_pb2_grpc.py、xxx_pb2.py 两个文件

3. Demo
    ``` Python 2.7
    import sys
    sys.path.insert(0, "./")
    import seckey_pb2
    import seckey_pb2_grpc
    import grpc
    er = seckey_pb2.EncryptRequest()
    print(dir(er.data))
    er.token = "testaes"
    er.data.append("test")
    er.data.append("test")
    # import pdb
    # pdb.set_trace()
    channel = grpc.insecure_channel('localhost:8081')
    stub = seckey_pb2_grpc.CryptStub(channel)
    print(dir(stub))
    rs = stub.DoEncrypt(er)
    print rs.data
    ```

## stream RPC

流式为什么要存在呢，是 Simple RPC 有什么问题吗？通过模拟业务场景，可得知在使用 Simple RPC 时，有如下问题：  
- 数据包过大造成的瞬时压力
- 接收数据包时，需要所有数据包都接受成功且正确后，才能够回调响应，进行业务处理（无法客户端边发送，服务端边处理）
