**数据类型**
|    json | golang interface{}   |
| --- | --- |
|   bool  | bool    |
|   string  | string    |
|  number   | float64    |
|  number   | json.Number    |
|   array  | []interface{}    |
|  object   | map[string]interface{}    |
|  null |  nil |

**编码不转义**
`SetEscapeHTML(false)`，编码时不转义`&` `<` `>`

**解码大整数**
`UseNumber()`，处理`interface{}`默认`float64`大整数精度丢失，使用`json.Number`，它实际上是个`string`。

**示例**
```
bbb := map[string]interface{}{
	"a": 144900168142032898,
	"b": "<json>",
	"c": 123.456,
}

// 不转义& < >
bsBuf := bytes.NewBuffer([]byte{})
encoder := json.NewEncoder(bsBuf)
encoder.SetEscapeHTML(false)
encoder.Encode(bbb)
fmt.Println(string(bsBuf.Bytes()))

// 处理大整数精度丢失，使用Number，它是个string
decoder := json.NewDecoder(bsBuf)
decoder.UseNumber()
decoder.Decode(&bbb)
fmt.Printf("%#v\n", bbb)
```
