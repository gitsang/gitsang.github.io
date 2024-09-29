
---

title: "interface struct 的 json 反序列化"
description: "通过创建临时结构体实现 struct 内 interface struct 的 json 反序列化"
lead: "通过创建临时结构体实现 struct 内 interface struct 的 json 反序列化"
date: 2021-09-24T13:55:01+08:00
lastmod: 2021-09-24T13:55:11+08:00
draft: false
images: []
menu:
  docs:
    parent: "golang"
weight: 100
toc: true

---

## 背景

```golang
type AData struct {
	A string `json:"a"`
}

type BData struct {
	B string `json:"b"`
}

type Message struct {
	Name string      `json:"name"`
	Id   int         `json:"id"`
	Data interface{} `json:"data"`
}
```

对于 `interface` 类型的数据很容易实现序列化(不需要任何额外步骤)

```golang
msgA := Message{
	Name: "msg_a",
	Id:   1,
	Data: AData{
		A: "a_data",
	},
}
msgB := Message{
	Name: "msg_b",
	Id:   2,
	Data: BData{
		B: "b_data",
	},
}

msgAJ, _ := json.Marshal(msgA)
log.Info("A", zap.Reflect("msgA", msgA), zap.ByteString("msgAJ", msgAJ))

msgBJ, _ := json.Marshal(msgB)
log.Info("B", zap.Reflect("msgB", msgB), zap.ByteString("msgBJ", msgBJ))
```

但 `interface` 反序列化后会变成 `map[string]interface` 类型，想要转成 `struct` 只能使用 `mapstructure` 之类的库

```golang
var msgX Message
_ = json.Unmarshal(msgAJ, &msgX)
log.Info("X", zap.Reflect("msgX", msgX), zap.Reflect("msgX.Data.A", msgX.Data.(AData).A))
// panic: interface conversion: interface {} is map[string]interface {}, not main.AData
```

此处是无法直接用 `msgX.Data.A` 来访问的，同样的 `msgX.Data.(AData).A` 也是不行的，因为这时候的 `data` 已经被反序列化成了 `map[string]interface`

### 解决方法 1

解决方法也很简单，只要再反序列化时能够知道需要反序列化成的类型即可。

在解析的时候定义临时 `struct` 继承 `Message` 并重新定义 Data 的类型。

```golang
msgXA := struct {
	*Message
	Data AData `json:"data"`
}{}
_ = json.Unmarshal(msgAJ, &msgXA)
log.Info("XA", zap.Reflect("msgXA", msgXA), zap.Reflect("msgXA.Data.A", msgXA.Data.A))

msgXB := struct {
	*Message
	Data BData `json:"data"`
}{}
_ = json.Unmarshal(msgBJ, &msgXB)
log.Info("XB", zap.Reflect("msgXB", msgXB), zap.Reflect("msgXB.Data.B", msgXB.Data.B))
```

### 解决方法 2

另一种思路是拆分 `struct` 每次序列化时将其合并，反序列化时再将其拆分[^1][^2]

缺点是每次需要传送两个变量

```golang
type ShortMessage struct {
	Name string `json:"name"`
	Id   int    `json:"id"`
}

func TestJsonStructSplit(t *testing.T) {
	msgA := ShortMessage{
		Name: "msg_a",
		Id:   1,
	}
	dataA := AData{
		A: "a_data",
	}

	msgB := ShortMessage{
		Name: "msg_b",
		Id:   2,
	}
	dataB := BData{
		B: "b_data",
	}

	// marshal

	msgAJ, _ := json.Marshal(struct {
		*ShortMessage
		*AData
	}{&msgA, &dataA})

	msgBJ, _ := json.Marshal(struct {
		*ShortMessage
		*BData
	}{&msgB, &dataB})

	// unmarshal

	var msgXA ShortMessage
	var dataXA AData
	_ = json.Unmarshal(msgAJ, &struct {
		*ShortMessage
		*AData
	}{&msgXA, &dataXA})

	var msgXB ShortMessage
	var dataXB BData
	_ = json.Unmarshal(msgBJ, &struct {
		*ShortMessage
		*BData
	}{&msgXB, &dataXB})
}
```

### 解决方法 3

只在反序列化时拆分[^1][^2]

缺点是 `Data` 实际上被解析了两次（一次解析成了 `map`，另一次解析成了 `struct`），而且每次使用时候还必须进行类型转换

```golang
var msgXA Message
var dataXA AData
_ = json.Unmarshal(msgAJ, &struct {
	*Message
	*AData `json:"data"`
}{&msgXA, &dataXA})
msgXA.Data = dataXA
t.Log("msgXA", msgXA, "data", msgXA.Data.(AData).A)

var msgXB Message
var dataXB BData
_ = json.Unmarshal(msgBJ, &struct {
	*Message
	*BData `json:"data"`
}{&msgXB, &dataXB})
msgXB.Data = dataXB
t.Log("msgXB", msgXB, "data", msgXB.Data.(BData).B)
```

## 完整测试代码

```golang
package main

import (
	"encoding/json"
	"testing"
)

type AData struct {
	A string `json:"a"`
}

type BData struct {
	B string `json:"b"`
}

type Message struct {
	Name string      `json:"name"`
	Id   int         `json:"id"`
	Data interface{} `json:"data"`
}

var msgA = Message{
	Name: "msg_a",
	Id:   1,
	Data: AData{
		A: "a_data",
	},
}

var msgB = Message{
	Name: "msg_b",
	Id:   2,
	Data: BData{
		B: "b_data",
	},
}

func TestJsonStruct(t *testing.T) {
	// marshal

	msgAJ, _ := json.Marshal(msgA)
	msgBJ, _ := json.Marshal(msgB)

	// unmarshal

	msgXA := struct {
		*Message
		Data AData `json:"data"`
	}{}
	_ = json.Unmarshal(msgAJ, &msgXA)
	t.Log("msgXA", msgXA, "data", msgXA.Data.A)

	msgXB := struct {
		*Message
		Data BData `json:"data"`
	}{}
	_ = json.Unmarshal(msgBJ, &msgXB)
	t.Log("msgXB", msgXB, "data", msgXB.Data.B)
}

type ShortMessage struct {
	Name string `json:"name"`
	Id   int    `json:"id"`
}

var msgAS = ShortMessage{
	Name: "msg_as",
	Id:   1,
}

var dataA = AData{
	A: "a_data",
}

var msgBS = ShortMessage{
	Name: "msg_bs",
	Id:   2,
}

var dataB = BData{
	B: "b_data",
}

func TestJsonStructSplit(t *testing.T) {
	// marshal

	msgAJ, _ := json.Marshal(struct {
		*ShortMessage
		*AData
	}{&msgAS, &dataA})

	msgBJ, _ := json.Marshal(struct {
		*ShortMessage
		*BData
	}{&msgBS, &dataB})

	// unmarshal

	var msgXA ShortMessage
	var dataXA AData
	_ = json.Unmarshal(msgAJ, &struct {
		*ShortMessage
		*AData
	}{&msgXA, &dataXA})
	t.Log("msgXA", msgXA, "data", dataXA.A)

	var msgXB ShortMessage
	var dataXB BData
	_ = json.Unmarshal(msgBJ, &struct {
		*ShortMessage
		*BData
	}{&msgXB, &dataXB})
	t.Log("msgXB", msgXB, "data", dataXB.B)
}

func TestJsonStructFull(t *testing.T) {
	// marshal

	msgAJ, _ := json.Marshal(msgA)
	msgBJ, _ := json.Marshal(msgB)

	// unmarshal

	var msgXA Message
	var dataXA AData
	_ = json.Unmarshal(msgAJ, &struct {
		*Message
		*AData `json:"data"`
	}{&msgXA, &dataXA})
	msgXA.Data = dataXA
	t.Log("msgXA", msgXA, "data", msgXA.Data.(AData).A)

	var msgXB Message
	var dataXB BData
	_ = json.Unmarshal(msgBJ, &struct {
		*Message
		*BData `json:"data"`
	}{&msgXB, &dataXB})
	msgXB.Data = dataXB
	t.Log("msgXB", msgXB, "data", msgXB.Data.(BData).B)
}
```

## 参考

[^1]: [Golang 中使用 JSON 的一些小技巧](https://zhuanlan.zhihu.com/p/27472716)
[^2]: [JSON and struct composition in Go](https://attilaolah.eu/2014/09/10/json-and-struct-composition-in-go/)
