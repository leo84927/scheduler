# scheduler
所有微服務的共同語言，服務間的設定鍵、訊息格式、資料型別都在這裡定義，任何修改都會影響多個服務

## Proto 檔案職責

| 檔案 | 職責 |
|---|---|
| `consul/kv.proto` | 所有服務的 Consul KV 鍵名定義，每個服務有各自的 enum |
| `rabbitmq/message.proto` | RabbitMQ 訊息的通用封裝格式（`Envelope`），跨服務共用 |
| `exchange_rate/currency.proto` | 貨幣與貨幣對的型別定義 |
| `email/gmail.proto` | Gmail label、郵件分類、清理收件匣的 message 定義 |

## module 路徑

| 用途 | 寫法 | 備注 |
|---|---|---|
| BSR module（push 目標） | `buf.build/leo84927-proto/scheduler` |  |
| go.mod | `require buf.build/gen/go/leo84927-proto/scheduler/protocolbuffers/go` | go get 自動產生 |
| Go import path | `import 別名 "buf.build/gen/go/leo84927-proto/scheduler/protocolbuffers/go/<go_package>"` | go_package 在各個 proto 檔的最上面 |

## 指令
```sh
# 引用/下載
go get buf.build/gen/go/leo84927-proto/scheduler/protocolbuffers/go
# 更新
go get -u buf.build/gen/go/leo84927-proto/scheduler/protocolbuffers/go

# 更新至 BSR
buf push
# 標記版本
buf push --label <version>
```

## 修改流程
修改時機通常為：
- 新增環境變數，在 consul/kv.proto 添加對應的 enum XxxEnvKey
- 若跨服務之間的 message queue 較複雜，使用 Envelope 時，視情況新增 EnvelopeType

修改完成後執行 buf push && buf push --label <version>  
各服務執行 go get -u

## Breaking Rule
根據 buf.yml 設定的 breaking 條件，若做出違反規則的改動，buf push 會被阻擋
