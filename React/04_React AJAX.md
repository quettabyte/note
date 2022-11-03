## React Ajax请求数据📚

* React本身只关注于界面, 并不包含发送ajax请求的代码

* 前端应用需要通过ajax请求与后台进行交互(json数据)

* react应用中需要集成第三方ajax库(或自己封装)

**常用的ajax请求库**📏

* JQuery：比较重, 如果需要另外引入不建议使用
* axios：轻量级，建议使用
  1. 封装XmlHttpRequest对象的ajax
  2. promise风格
  3. 可以用在浏览器端和node服务器端

> 一般网络请求放在componentDidMount函数里

### axios用法

**get请求**

```js
axios.get('/user?ID=12345').then(
    res => {
        console.log(res.data);
    },
    err => {
    	console.log(err);
    }
)
```

**post请求**

```js
axios.get('/user?ID=12345',{
    firstName: 'Fred',
    lastName: 'Flintstone'
}).then(
    res => {
        console.log(res.data);
    }
).catch(
	err => {
        console.log(err);
    }
);
```

### 扩展：Fetch

* 原生函数，不再使用XHR对象提交ajax请求
* 老版本浏览器可能不支持

**GET请求**

```js
fetch(url).then(
    res => {
    	return response.json()
    }
).then(
    data => {
    console.log(data)
    }
).catch(
    err => {
        console.log(e)
    }
);
```

**POST请求**

```js
fetch(url, {
    method: "POST",
    body: JSON.stringify(data),
}).then(
    data => {
    	console.log(data);
  	}
).catch(
    err => {
    	console.log(e);
    }
);
```

使用`asyna`、`await`实现

```js
search = async() => {
    const {keyWordElement:{value:keyWord}} = this
    PubSub.publish('atguigu',{isFirst:false,isLoading:true})
    try {
      const response= await fetch(`/api1/search/users2?q=${keyWord}`)
        const data = await response.json()
        console.log(data);
        PubSub.publish('atguigu',{isLoading:false,users:data.items})
    } catch (error) {
        console.log('请求出错',error);
        PubSub.publish('atguigu',{isLoading:false,err:error.message})
    }
}
```







