```
package main

import (
   "fmt"
   "github.com/elastic/go-elasticsearch/v7"
   "github.com/elastic/go-elasticsearch/v7/esapi"
   "io/ioutil"
   "log"
   "net/http"
)

func main() {
   cfg := elasticsearch.Config{
      Addresses: []string{"http://192.168.126.129:9200/"},
      Transport: &http.Transport{
         MaxIdleConnsPerHost: 10,
      },
   }

   es, err := elasticsearch.NewClient(cfg)
   if err != nil {
      log.Printf("Error creating the es: %s", err)
   } else {
      //log.Println(es.Info())
   }

   var resp *esapi.Response

   // ------------------------------ 增删改查 ------------------------------
   resp, err = es.Cat.Indices(es.Cat.Indices.WithPretty())

   //resp, err = es.Search(es.Search.WithIndex("user"), es.Search.WithPretty())

   //resp, err = es.Count(es.Count.WithIndex("user"), es.Count.WithPretty())

   //resp, err = es.Get("user", "12", es.Get.WithPretty())

   // resp, err = es.Search(es.Search.WithIndex("user"), es.Search.WithBody(bytes.NewBuffer([]byte(`{
   //  "query": {
   //    "term": {
   //      "name": "born"
   //    }
   //  }
   //}`))), es.Search.WithPretty())

   // resp, err = es.Index("user", bytes.NewBuffer([]byte(`{
   //  "name": "born",
   //  "info": {
   //    "age": 18
   //  },
   // "desc": "he is strong and kind, everyone knows."
   //}`)), es.Index.WithPretty(), es.Index.WithDocumentID("1"))

   //resp, err = es.Create("user", "10", bytes.NewBuffer([]byte(`{
   // "name": "born",
   // "info": {
   //   "age": 18
   // },
   // "desc": "he is strong and kind, everyone knows."
   //}`)), es.Create.WithPretty())

   // resp, err = es.Bulk(bytes.NewBuffer([]byte(`
   //{"index":{"_id":12}}
   //{"name": "小红红","info": {"age": 22}}
   //{"index":{"_id":13}}
   //{"name": "小绿绿","info": {"age": 23}}
   //`)), es.Bulk.WithIndex("user"), es.Bulk.WithPretty())

   // resp, err = es.Update("user", "10", bytes.NewBuffer([]byte(`{
   //  "doc":{
   //    "info": {
   //      "age": 28
   //    }
   //  }
   //}`)), es.Update.WithPretty())

   //resp, err = es.Delete("user", "10", es.Delete.WithPretty())

   //resp, err = es.Indices.Create("worker", es.Indices.Create.WithPretty())

   //resp, err = es.Indices.Delete([]string{"worker"}, es.Indices.Delete.WithPretty())

   // ------------------------------ map操作 ------------------------------
   //resp, err = es.Indices.PutMapping(bytes.NewBuffer([]byte(`{
   // "properties": {
   //   "name": {
   //     "type": "keyword"
   //   },
   //   "info.age": {
   //     "type": "integer"
   //   },
   //   "desc": {
   //     "type": "text"
   //   }
   // }
   //}`)), es.Indices.PutMapping.WithIndex("worker"), es.Indices.PutMapping.WithPretty())

   respFormat(resp)
}

func respFormat(resp *esapi.Response) {
   body, err := ioutil.ReadAll(resp.Body)
   if err != nil {
      fmt.Println(err)
   }
   resp.Body.Close()
   fmt.Println(string(body))
}
```