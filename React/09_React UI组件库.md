## React UI组件库📚

### Ant Design

> 以下配置是 `3.x` 版本，`4.x` 版本见[官网](https://3x.ant.design/index-cn)

1. 安装依赖：

   ```shell
   npm install react-app-rewired customize-cra babel-plugin-import less less-loader
   ```

2. 修改 `package.json`

   ```json
   "scripts": {
     "start": "react-app-rewired start",
     "build": "react-app-rewired build",
     "test": "react-app-rewired test",
     "eject": "react-scripts eject"
   }
   ```

3. 根目录下创建 `config-overrides.js`

   ```js
   //配置具体的修改规则
   const { override, fixBabelImports, addLessLoader } = require('customize-cra')
   
   module.exports = override(
     fixBabelImports('import', {
       libraryName: 'antd',
       libraryDirectory: 'es',
       style: true,
     }),
     addLessLoader({
       lessOptions: {
         javascriptEnabled: true,
         modifyVars: { '@primary-color': 'green' },
       },
     })
   )
   ```

4. 组件里引用

   ```jsx
   import { Button,DatePicker } from 'antd';
   import {WechatOutlined,WeiboOutlined,SearchOutlined} from '@ant-design/icons'
   const { RangePicker } = DatePicker;
   
   export default class App extends Component {
   	render() {
   		return (
   			<div>
   				App....
   				<button>点我</button>
   				<Button type="primary">按钮1</Button>
   				<Button >按钮2</Button>
   				<Button type="link">按钮3</Button>
   				<Button type="primary" icon={<SearchOutlined />}>
   					Search
   				</Button>
   				<WechatOutlined />
   				<WeiboOutlined />
   				<DatePicker/>
   				<RangePicker/>
   			</div>
   		)
   	}
   }
   ```

   