1. 全局安装 `json-server`

   ```shell
   npm install -g json-server
   ```

2. 创建 `db.json` 文件，并写一些数据

   ```json
   {
     "posts": [
       { "id": 1, "title": "json-server", "author": "typicode" }
     ],
     "comments": [
       { "id": 1, "body": "some comment", "postId": 1 }
     ],
     "profile": { "name": "typicode" }
   }
   ```

3. 启动 `JSON Server`

   ```shell
   json-server --watch db.json --port 8000
   ```

### API用法

取数据： `get`

```js
axios.get("http://localhost:8000/posts/1").then(res=>{
  console.log(res.data)
})
```

增加数据：`post`

```js
axios.post("http://localhost:8000/posts",{
  // 有id自增长功能
  title: "33333",
  author: "xiaoming"
})
```

更新数据：`put`、`patch`

```js
// put会直接替换原来的对象
axios.put("http://localhost:8000/posts/1",{
  title: "1111-修改"
})
// patch补丁式修改
axios.patch("http://localhost:8000/posts/1",{
  title: "1111-修改"
})
```

删除数据：`delete`

```js
axios.delete("http://localhost:8000/posts/1")
```

### 其他用法

实例数据

```json
{
  "posts": [
    {
      "id":1,
    	"title": "1111",
      "author": "cokeice"
    },
    {
      "id":2,
    	"title": "2222",
      "author": "tiechui"
    },
  ],
  "comments": [
    {
      "id":1,
    	"body": "1111-comment",
      "postId": 1
    },
    {
      "id":2,
    	"body": "2222-comment",
      "postId": 2
    }
  ]
}
```

#### 删除关联

```js
axios.delete("http://localhost:8000/posts/1")
//删除后，comments中postId为1的也会被关联删除
```

#### 向下关联数据

`_embed`

```js
axios.get("http://localhost:8000/posts?_embed=comments").then(res=>{
  console.log(res.data)
})
```

#### 向上关联数据

`_expand`

```js
axios.get("http://localhost:8000/posts?_expand=post").then(res=>{
  console.log(res.data)
})
```

