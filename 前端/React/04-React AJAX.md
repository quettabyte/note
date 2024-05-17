## React Ajax请求数据📚

*   React本身只关注于界面, 并不包含发送ajax请求的代码

*   前端应用需要通过ajax请求与后台进行交互(json数据)

*   react应用中需要集成第三方ajax库(或自己封装)

**常用的ajax请求库**📏

*   JQuery：比较重, 如果需要另外引入不建议使用
*   axios：轻量级，建议使用
    1.  封装XmlHttpRequest对象的ajax
    2.  promise风格
    3.  可以用在浏览器端和node服务器端

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

### react脚手架代理

1.  在 `package.json` 文件中进行配置：

```json
"proxy": "http://localhost:5000"
```

*   优点：配置简单，前端请求资源可不加前缀
*   缺点：不能配置多个代理
*   工作方式：当请求了 3000 端口号（本机）不存在的资源时，就会把请求转发给 5000 端口号服务器

1.  在 `src` 目录下创建代理配置文件 `setupProxy.js` ，进行配置：

```shell
npm install http-proxy-middleware -S
```

```js
const proxy = require('http-proxy-middleware')

module.exports = function (app) {
  app.use(
    //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
    proxy('/api1', {
      //配置转发目标地址(能返回数据的服务器地址)
      target: 'http://localhost:5000',
      //控制服务器接收到的请求头中host字段的值
      /*
      changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
      changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
      changeOrigin默认值为false，但一般将changeOrigin改为true
      */
      changeOrigin: true,

      //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
      pathRewrite: { '^/api1': '' },
    }),
    proxy('/api2', {
      target: 'http://localhost:5001',
      changeOrigin: true,
      pathRewrite: { '^/api2': '' },
    })
  )
}
```

### 新版代理写法

上面的写法已经被淘汰

现在使用的是 `createProxyMiddleware` 模块

```js
const { createProxyMiddleware } = require('http-proxy-middleware')

module.exports = function (app) {
 app.use(
  createProxyMiddleware('/api1', {
   target: 'http://localhost:5000', 
   changeOrigin: true, 
   pathRewrite: { '^/api1': '' }, 

  }),
  createProxyMiddleware('/api2', {
   target: 'http://localhost:5001',
   changeOrigin: true,
   pathRewrite: { '^/api2': '' },
  })
 )
}
```

### 扩展：Fetch

*   原生函数，不再使用XHR对象提交ajax请求
*   老版本浏览器可能不支持

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

