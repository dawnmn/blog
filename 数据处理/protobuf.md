

`Protocol buffers`是一种语言中立，平台无关，可扩展的序列化数据的格式，可用于通信协议，数据存储等，采用压缩的二进制编码。

```
syntax = "proto3";

package proto;
option go_package="./proto";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

**分配字段编号**
每个消息定义中的每个字段都有唯一的编号，1~15字段编号需要一个字节进行编码，16~2047需要两个字节。

**允许嵌套**
```
message SearchResponse {
  repeated Result results = 1;
}

message Result {
  string url = 1;
  string title = 2;
  repeated string snippets = 3;
}
```

**数组**
repeated 类型可以用来表示数组

**与go的struct相互转换**
```
out, err := proto.Marshal(&message)
proto.Unmarshal(out, &message)
```

**go的struct与json相互转换**
```
package main

import (
	"fmt"
	"github.com/golang/protobuf/jsonpb"
	"hello/proto"
)

func main() {
	message := &proto.TaskId{
		TaskId: 100,
	}

	marshaller := jsonpb.Marshaler{}
	jsonStr, _ := marshaller.MarshalToString(message)
	fmt.Println(jsonStr)

	jsonpb.UnmarshalString(jsonStr, message)
	fmt.Println(message)
}
```

**命名规范**
message名称采用驼峰命名法，并且首字母大写开头。字段名采用下划线分隔法命名。

**Base 128 Varints编码**
用一个或多个字节来表示一个数字，值越小的数字使用越少的字节数。

**Message Structure 编码**
message 的二进制版本采用了tag-value的形式，只是使用字段号作为 key。每个字段的名称和声明类型只能在解码端通过引用消息类型的定义（即 .proto 文件）来确定。

key 的计算方法是` (field_number << 3) | wire_type`

wire_type 类型为 2 的数据，是一种指定长度的编码方式：`key + length + content`，key 的编码方式是统一的，length 采用 varints 编码方式，content 就是由 length 指定长度的 Bytes

编码示例：
```
message Test2 {
  optional string b = 2;
}
```
设置该值为"testing"，二进制格式查看：
`12 07 74 65 73 74 69 6e 67`
`74 65 73 74 69 6e 67` 是“testing”的 UTF8 代码。
此处，key 是16进制表示的，所以展开是：
`12` -> `0001 0010`，后三位 010 为 `wire type = 2`，`0001 0010` 右移三位为 `0000 0010`，即 tag = 2。
length 此处为 7，后边跟着 7 个bytes，即我们的字符串"testing"。

protocol buffers采用压缩的二进制编码
