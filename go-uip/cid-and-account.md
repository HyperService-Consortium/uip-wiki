# Chain ID, Address and Account

## Chain ID

每一条链都被标记为唯一一个链的编号，链编号长度为8字节。例如，当链编号为`0.0.0.3`时，这一串数字唯一指定了整个网络中的NSB链。

## Address

`chain_id`已经唯一地确认了一条区块链实体，进一步`address`字段将唯一地确认该链上某个账户。为了通用表示，`address`是一个变长的字节数组。`address`字段相等当且仅当两个字节数组长度相等且每个位置的字节值相等。

## Account

go-uip规定一个有意义的Account是以下二元组：

+ `uint64(8 octets)`: `chain-id`
+ `octets`: `address`

它唯一地表示了NSB区块链网络中某一条区块链上的某个账户，这个账户可以是某一个合约账户，也可以是用于交易的账户。

### 传输格式

#### 1. 变长地址

可以按照以下格式压缩：

| 8 octets | 2 octets | octets  |
| :------: | :------: | :-----: |
| chain-id |   len    | address |

#### 2. 定长地址（根据chain-id确认）

一般每一条链上有且仅有一种地址的格式，因此address总不是变长的。所以也可以按照以下格式压缩：

| 8 octets | octets  |
| :------: | :-----: |
| chain-id | address |

当chain-id增加时，这种压缩方式会有问题。

#### 3.易于阅读的格式

网络传输格式和存储格式不影响系统的运行，为图方便，也可以使用json格式表示一个Account：

```json
{
	"address": "<string>",
	"chain_id": "<uint64>",
}
```

#### 4. GRPC传输

grpc定义示例参考[go-ves代码](https://github.com/HyperService-Consortium/go-ves/blob/7fa341a/grpc/uiprpc-base/base.pb.go#L188)