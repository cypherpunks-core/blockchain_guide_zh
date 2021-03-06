## 鏈碼示例一：信息公證
### 簡介

[chaincode_example01.go](chaincode_example01.go) 主要實現如下的功能：

* 初始化，以鍵值形式存放信息；
* 允許讀取和修改鍵值。

代碼中，首先初始化了 `hello_world` 的值，並根據請求中的參數創建修改查詢鏈上 `key` 中的值，本質上實現了一個簡單的可修改的鍵值數據庫。

### 主要函數

* `read`：讀取key `args[0]` 的 value；
* `write`：創建或修改 key `args[0]` 的 value；
* `init`：初始化 key `hello_world` 的 value；
* `invoke`：根據傳遞參數類型調用執行相應的 `init` 和 `write` 函數；
* `query`：調用 `read` 函數查詢 `args[0]` 的 value。

### 代碼運行分析

`main` 函數作為程序的入口，調用 shim 包的 start 函數，啟動 chaincode 引導程序的入口節點。如果報錯，則返回。

```go
func main() {
    err := shim.Start(new(SimpleChaincode))
    if err != nil {
        fmt.Printf("Error starting Simple chaincode: %s", err)
    }
}
```

當智能合約部署在區塊鏈上，可以通過 rest api 進行交互。

三個主要的函數是 `init`，`invoke`，`query`。在三個函數中，通過 `stub.PutState`與 `stub.GetState` 存儲訪問 ledger 上的鍵值對。

### 通過 REST API 操作智能合約

假設以 jim 身份登錄 pbft 集群，請求部署該 chaincode 的 json 請求格式為：
```json
{
    "jsonrpc": "2.0",
    "method": "deploy",
    "params": {
        "type": 1,
        "chaincodeID": {
            "path": "https://github.com/ibm-blockchain/learn-chaincode/finished"
        },
        "ctorMsg": {
            "function": "init",
            "args": [
                "hi there"
            ]
        },
        "secureContext": "jim"
    },
    "id": 1
}
```

目前 path 僅支持 github 上的目錄，ctorMsg 中為函數 `init` 的傳參。

調用 invoke 函數的 json 格式為：

```json
{
    "jsonrpc": "2.0",
    "method": "invoke",
    "params": {
        "type": 1,
        "chaincodeID": {
            "name": "4251b5512bad70bcd0947809b163bbc8398924b29d4a37554f2dc2b033617c19cc0611365eb4322cf309b9a5a78a5dba8a5a09baa110ed2d8aeee186c6e94431"
        },
        "ctorMsg": {
            "function": "init",
            "args": [
                "swb"
            ]
        },
        "secureContext": "jim"
    },
    "id": 2
}
```

其中 name 字段為 `deploy` 後返回的 message 字段中的字符串。

`query` 的接口也是類似的。
