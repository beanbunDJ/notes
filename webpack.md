# 1 webpack

## 1.1 打包工具

- 工具：grunt、gulp

  称为任务执行器，将所有文件拼接在一起，通过js的立即调用函数表达式（IIFE）解决作用域问题

- webpack、parcel、rollup.js、vite

## 1.2 模块化

- commonJS引入require的机制

  CommonJS是2009年由JavaScript社区提出的包含了模块化的一个标准，后来被Node.js所采用并实现，在Node.js中用到的模块导入导出都是依照CommonJS标准来实现的

  ```javascript
  //注意：export和require只能在node.js下运行，不能在浏览器运行
  //解决方法：使用requireJS库
  //app.js 导出模块
  const add = (x,y)=>{
      return x+y;
  }
  
  const minus = (x,y)=>{
      return x-y;
  }
  
  module.exports = {
      add,
      minus
  }
  //index.js
  const math = require('./app.js')
  console.log(math.add(1,2));
  ```

- ECMAScript module定义和导出模块

  ```javascript
  //终端输入命令快速启动一个http服务：npx http-server
  //add.js
  const add = (x,y)=>{
      return x+y;
  }
  export default add
  
  // minus.js
  const minus = (x,y)=>{
      return x-y;
  }
  
  export default minus
  //index.html 引入
  <script type="module">
     import add from './esm/add.js'
  	 console.log(add(1,2));
  </script>
  ```

## 1.3 webpack安装

> 需要安装webpack（主包）和webpack-cli（作用是，在命令行能够执行webpack相关命令）
>
> 注意：mac中全局安装可能需要权限，因此需要执行sudo npm install webpack webpack-cli -g

- 本地全局安装`npm install webpack webpack-cli -g`

- 工作目录安装`npm init -y`;`npm install webpack webpack-cli --save-dev`,其中--save-dev表示开发环境中安装（即放在package.json下的devDependencies中）如果没有加--save-dev，默认是--save，则放在dependencies下

  ```json
  {
    "name": "webpack002",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    “dependencies”:{},
    "devDependencies": {
      "webpack": "^5.70.0",
      "webpack-cli": "^4.9.2"
    }
  }
  
  ```

## 1.4 运行webpack

### 1.4.1 NPM - 管理Node包的工具

- npm5.2+支持NPX

- NPM本身实际上不能运行任何包，如果你想去执行某个包，必须在package.json文件中去定义。

当通过NPM安装Node包，NPM会在node_modules下的.bin里创建一个软连接。

- 本地安装链接会放到./node_modules/.bin目录

- 全局安装会连接到全局的文件，linux/macos在/usr/local/bin里，windows则在/AppData/npm

安装到特定项目下的Node包，执行:`npm install some-package`

现在如果像下面这样去执行some-package会失败的。只有全局安装的包才可以只用包名去执行。

`$ some-package`

为了解决这个问题，必须像下面这样，将本地包的相对项目的路径填写全。

`$ ./node_modules/.bin/some-package`

当然如果你非要通过命令去执行的话，也不是不可以的。通过编辑package.json文件，将以下脚本添加到script属性下

```
{ 
   "name": "whatever", 
  "version": "1.0.0", 
  "scripts": {  
    "some-package": "some-package" 
   }
}
```

然后通过`npm run some-package`调用。

### 1.4.2 NPX - 执行Node包的工具

npx会检查命令（<some-package>）是否出现在$PATH,或本地项目中，然后去执行。

所以，对于上面的例子，如果想通过在本地执行，可以通过npx some-package执行。

npx some-package

**npx另一个重要的优势是，可以执行未安装的包的命令**，例如：

$ npx create-react-app my-app

上面的例子将会创建一个名为my-app的React的工程，创建的位置就在输入命令的当前文件夹，并且保证永远使用最新的构建工具，而不用担心升级的问题。而如果不用npx的话，你需要首先通过npm安装create-react-app包才可以create-react-app my-app，并且一旦create-react-app升级了你都需要执行create-react-app的升级。

> 注意：如果未在全局安装webpack，则需要指定项目的node_module目录：node_modules/.bin/webpack

- 已全局安装：进入项目根目录，运行`webpack`
- 未全局安装：进入项目根目录，运行`node_modules/.bin/webpack`
- 未全局安装：进入项目根目录，运行`npx webpack`

### 1.4.3 目录结构

> 运行完成后会多出一个dist目录
>
> 查看详细打包信息命令：webpack --stats detailed
>
> 默认打包src下，index.js为入口文件

```yaml
- dist
  - main.js
```

## 1.5 自定义webpack

> 执行命令npx webpack --help可查看所有支持命令

- 通过命令行自定义

- 通过配置文件：webpack.config.js文件来自定义（注意：该文件名不可变更）

  ```json
  //引入node中的path模块
  const path = require('path')
  //该文件在nodejs中运行，所以使用commonjs模块
  module.exports = {
      //入口文件配置
      entry:'./src/index.js', 
      //出口配置
      output:{
          //出口文件名,也可以为路径，例如打包到JS目录下的bundle.js
          filename:'bundle.js',
          //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
          path:path.resolve(__dirname,'./dist')
      },
  
      mode:'none'
  }

## 1.6 自动引入资源

> webpack支持的插件：https://webpack.docschina.org/plugins/

- 安装插件`npm install html-webpack-plugin -D`并在webpack.config.js中引入

  ```json
  //引入node中的path模块
  const path = require('path')
  
  //导入插件
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  //该文件在nodejs中运行，所以使用commonjs模块
  module.exports = {
      //入口文件配置
      entry:'./src/index.js', 
      //出口配置
      output:{
          //出口文件名
          filename:'bundle.js',
          //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
          path:path.resolve(__dirname,'./dist'),
          //生成dist时，自动清理上次打包遗留文件
          clean: true
      },
      /*
      打包模式
      1.development打包后，一些没有依赖的方法 变量 文件会保留，production则会移除。
      2.production打包后，代码会进行压缩，比development的文件小。
      */
      mode:'development',
      //方便代码定位错误，指向真实代码出错行数
      devtool: 'inline-source-map',
      //配置使用插件
      plugins:[
          //插件作用：打包出html自动引入打包后的资源包，例如：bundle.js
          new HtmlWebpackPlugin({
              //指定生成的.html入口文件模版（基于配置中的html生成打包后的html文件）
              template:'./index.html',
              //指定打包出来的文件名
              filename:'app.html',
              //自动引入资源的位置，放于body标签内
              inject:'body'
          })
      ],
  }
  ```

## 1.7 热更新

> 监听代码变更，实时刷新浏览器

- 执行命令：`npx webpack --watch`,监听代码变更自动编译

- 安装插件：`npm install webpack-dev-server -D`

- 安装后，在webpack.config.js中进行配置devServer监听打包后代码，热更新（浏览器自动刷新）

- 执行命令`npx webpack server`

  ```json
  //引入node中的path模块
  const path = require('path')
  
  //导入插件
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  //该文件在nodejs中运行，所以使用commonjs模块
  module.exports = {
      //入口文件配置
      entry:'./src/index.js', 
      //出口配置
      output:{
          //出口文件名
          filename:'bundle.js',
          //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
          path:path.resolve(__dirname,'./dist'),
          //生成dist时，自动清理上次打包遗留文件
          clean: true
      },
    
      ...
  
      //配置热更新物理路径
      devServer:{
          static:'./dist'
      }
  }
  ```

## 1.8 资源模块

- assets/resource：发送一个单独的文件并导出 URL

- assets/inline：导出data url（base64格式）

- assets/source：导出资源源代码

- assets：通用资源类型，在assets/resource和assets/inline之间做选择，默认大于8k会自动选择asset/resource，调整选择临界值在webpack.config.js中设置`parser`

  例如引入了图片资源，如何打包？svg如何打包？	.txt文件如何打包？

  ```js
  //index.js
  import helloword from './hello-word'
  import imgsrc from './assets/a.png'
  import svgsrc from './assets/a.svg'
  //引用png文件
  const img = document.createElement('img')
  img.src = imgsrc;//imgsrc必须为一个路径
  document.body.appendChild(img);
  //引用svg文件
  const img1 = document.createElement('img')
  img.src = svgsrc;
  document.body.appendChild(img1);
  //引用txt文件
  const div = document.createElement('div')
  div.textContent = txt;
  document.body.appendChild(div);
  ```

> 更改webpack.config.js配置中的module

```json
//引入node中的path模块
const path = require('path')
module.exports = {
    //入口文件配置
    entry:'./src/index.js', 
    //出口配置
    output:{
        //出口文件名
        filename:'bundle.js',
        //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
        path:path.resolve(__dirname,'./dist'),
        //生成dist时，自动清理上次打包遗留文件
        clean: true,
        //配置资源模块打包后的路径，[contenthash]表示根据文件内容生成一个哈希字符串，[ext]表示使用原扩展名
        assetModuleFilename:'images/[contenthash][ext]'
    },
  
   ...

    //资源模块
    module:{
        //根据不同的规则，加载不同的资源文件
        rules:[
            {
                //根据正则匹配以png结尾的文件
                test:/\.png$/,
                type:'asset/resource',
                /*
                配置资源模块打包后的路径，[contenthash]表示根据文件内容生成一个哈希字符串，[ext]表示使用原扩展名
                优先级高于output中assetModuleFilename配置
                */
                generator:{
                    filename: 'imgs/[contenthash][ext]'
                }
            },
            {
                test:/\.svg$/,
                /**
                 * @description:导出data url(base64的格式)
                 */
                type:'asset/inline',
            },
            {
                test:/\.txt$/,
                /**
                 * @description:导出资源的源代码
                 */
                type:'asset/source',
            },
            {
                test:/\.jpg$/,
                /**
                 * @description:通用资源类型，打包路径没配置默认继承output中assetModuleFilename
                 * @description:会在asset/inline和asset/resource中选择，默认当大于8k选择asset/resource
                 * @description:可调整选择临界值
                 */
                type:'asset',
                parser:{
                    //设置选择asset/inline（data url）的条件，当小于1*1024*1024（1m）则选择asset/inline
                    dataUrlCondition:{
                        maxSize: 1*1024*1024
                    }
                }
            }
        ]
    }
}
```

## 1.9 Loader

> webpack只能识别js、json的能力
>
> Loader的作用是让webpack拥有了加载和解析非JavaScript文件的能力
>
> 不同的loader能解析对应不同的资源文件，成为一个有效的模块供给webpack打包

### 1.9.1 样式文件

> 用于处理css文件，打包成有效模块
>
> 注意：css-loader只用于处理css相关文件，但是并不能让样式生效，样式生效需要额外使用style-loader
>
> 若有其他预处理工具，需要安装相应的loader

- 安装css-loader： `npm install css-loader -D`

- 安装style-loader：`npm install style-loader -D`

- 若需要使用less：`npm isntall less-loader less -D`

- 若需使用sass：`npm isntall sass-loader sass -D`

- 在webpack.config.js配置以下内容

  ```json
  //资源模块
      module:{
          //根据不同的规则，加载不同的资源文件
          rules:[
              {
                  test:/\.(css|less)$/,
                  /**
                   * @description:css-loader:用于处理css文件变成有效模块,style-loader:将css放在页面上
                   * @description:顺序不可颠倒，加载顺序是从右向左
                   * @description:将less或sass编译成css文件，再通过css-loader处理css文件，最后由style-loader引入页面
                   */
                  use:['style-loader','css-loader','less-loader'],
              }
          ]
      }
  ```

### 1.9.2 样式文件压缩

> 插件：npm install mini-css-extract-plugin:抽离css相关代码导出单独一个文件
>
> 注意：该插件在webpack5环境下才可以使用
>
> 插件：npm install css-minimizer-webpack-plugin:将css代码进行压缩
>
> 当使用了压缩css进行引入时，就不再需要style-loader
>
> 注意：光使用压缩还需要动态引入压缩后的css文件，需要插件HtmlWebpackPlugin，具体可查看1.6章节

- 安装插件mini-css-extract-plugin
- 更改webpack.config.js中plugins配置
- 安装插件css-minimizer-webpack-plugin
- 更改webpack.config.js中optimization配置
- 将打包模式改为production

```json
//引入node中的path模块
const path = require('path')

//导入插件
/**
 * @description:HtmlWebpackPlugin:动态生成html，将打包好的js和css引入入口html文件
 * @description：MiniCssExtractPlugin:将所有css进行合并输出
 */
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

//该文件在nodejs中运行，所以使用commonjs模块
module.exports = {
  ...
    /*
    打包模式
    1.development打包后，一些没有依赖的方法 变量 文件会保留，production则会移除。
    2.production打包后，代码会进行压缩，比development的文件小。
    */
    mode:'production',
    //方便代码定位错误，指向真实代码出错行数
    devtool: 'inline-source-map',
    //配置使用插件
    plugins:[
        //插件作用：打包出html自动引入打包后的资源包，例如：bundle.js
        new HtmlWebpackPlugin({
            //指定生成的.html入口文件模版（基于配置中的html生成打包后的html文件）
            template:'./index.html',
            //指定打包出来的文件名
            filename:'app.html',
            //自动引入资源的位置，放于body标签内
            inject:'body'
        }),
        new MiniCssExtractPlugin({
            filename:'styles/[contenthash].css'
        })
    ],

  
    //资源模块
    module:{
        //根据不同的规则，加载不同的资源文件
        rules:[
            {
                test:/\.(css|less)$/,
                /**
                 * @description:css-loader:用于处理css文件变成有效模块,style-loader:将css放在页面上
                 * @description:顺序不可颠倒，加载顺序是从右向左
                 * @description:将less或sass编译成css文件，再通过css-loader处理css文件，最后由style-loader引入页面
                 */
                // use:['style-loader','css-loader','less-loader'],
                use:[MiniCssExtractPlugin.loader,'css-loader','less-loader'],
            }
        ]
    },

    //优化配置
    optimization:{
        minimizer:[
            new CssMinimizerPlugin()
        ]
    }
}
```

### 1.9.3 加载字体资源

- webpack.config.js

```json
 //资源模块
    module:{
        //根据不同的规则，加载不同的资源文件
        rules:[
            //加载字体资源
            {
                test:/\.(woff|woff2|eot|ttf|otf)$/,
                /**
                 * @description:asset/resource可以处理任意类型资源
                 */
                // use:['style-loader','css-loader','less-loader'],
                type:'asset/resource',
            }
        ]
    },

```

- Styles.css文件引入

```css
@font-face{
  /*自定义字体类型*/
  font-family:'iconfont';
  /*引入字体文件路径*/
  src:url('./assets/iconfont.ttf').format('truetype');
}

.icon{
  font-family:'iconfont';
  font-size:24px;
}
```

### 1.9.4 加载数据

> 常见数据文件格式有json、xml、csv、tsv等等，其中json为webpack本身支持，其他需要借助loader完成

- 安装loader：`npm install csv-loader xml-loader -D`
- 修改webpack.config.js配置

```json
 //资源模块
    module:{
        rules:[
            {
                test:/\.(csv|tsv)$/,
                use:'csv-loader',
            },
           {
                test:/\.xml$/,
                use:'xml-loader',
            }
        ]
    },
```

### 1.9.5 自定义json模块parser

> 安装三个包npm install toml yaml json5 -D

- Yaml：有key和value通过缩紧完成子项设置

  ```yaml
  a: 123
  b: 
    c: 456
  ```

  

- toml：有key和value，如果有子项通过中括号方式来定义

  ``` toml
  a = 123
  [b]
  c = 456
  ```

- Json5：对比传统的json格式，key不再需要双引号，value也可以使用单引号或者换行，同时也可以加注释

  ```json
  {
    a: 123
    b:{
      //这是子项
      c:456
    }
  }
  ```

- 修改webpack.config.js配置

  ```json
  //引入node中的path模块
  const path = require('path')
  
  //导入插件
  /**
   * @description:HtmlWebpackPlugin:动态生成html，将打包好的js和css引入入口html文件
   * @description：MiniCssExtractPlugin:将所有css进行合并输出
   */
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
  
  const toml = require('toml')
  const yaml = require('yaml')
  const json5 = require('json5')
  
  //该文件在nodejs中运行，所以使用commonjs模块
  module.exports = {
    
      ...
  
      //资源模块
      module:{
          //根据不同的规则，加载不同的资源文件
          rules:[
              //加载toml格式json
              {
                  test:/\.toml$/,
                  type:'json',
                  parser:{
                      parse: toml.parse
                  }
              },
              //加载yaml格式json
              {
                  test:/\.yaml$/,
                  type:'json',
                  parser:{
                      parse: yaml.parse
                  }
              },
              //加载json5格式json
              {
                  test:/\.json5$/,
                  type:'json',
                  parser:{
                      parse: json5.parse
                  }
              }
          ]
      }
  }
  ```

  

  ### 1.9.6 babel-loader

  > npm install -D babel-loader @babel/core @babel/preset-env
  >
  > npm install -D @babel/runtime @babel/plugin-transform-runtime
  >
  > `regeneratorRuntime`：时webpack打包生成的全局辅助函数，由babel生成，用于兼容async/await语法

- `Babel-loader`:在webpack里面应用babel解析es6的桥梁

- `@babel/core`：babel核心模块

- `@babel/preset-env`：babel预设，一组babel插件的合集

- `@babel/runtime`：包含了regeneratorRuntime运行时的内容

- `@babel/plugin-transform-runtime`：会在需要regeneratorRuntime的地方自动导包

- 修改webpack.config.js配置

  ```json
  //解析js的es6语法
  {
    test:/\.js$/,
    //排除node_modules下的文件不需要编译
    exclude:/node_modules/,
    use:{
      loader:'babel-loader',
      options:{
        presets:['@babel/preset-env'],
        plugins:[
          [
            '@babel/plugin-transform-runtime'
          ]
        ]
      }
    }
  }
  ```

## 1.10 代码分离

> 能够打包出不同的bundle.js，让我们能够按需加载，控制资源的优先级

- 配置入口节点
- 防止重复：使用Entry dependencies或者SplitChunksPlugin去重和分离代码
- 动态导入：通过模块的内联函数调用来分离代码

### 1.10.1 配置入口节点

> 使用entry配置手动分离代码，弊端，如果多个入口，多个入口共享文件将会重复打包，例如：a.js和b.js都引入了jquery库，那么jquery将会被打包进两个文件中

```json
 module.exports = {
   //入口文件配置
    //entry:'./src/index.js', 
    entry:{
        index:'./src/index.js', 
        test:'./src/test.js'
    },
    //出口配置
    output:{
        /**
         * 出口文件名
         * @description:单文件输出可以例如：filename:'bundle.js',
         * @description:多个入口文件配置可以使用[name]获取对应的入口文件名
         */
        filename:'[name].bundle.js',
        //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
        path:path.resolve(__dirname,'./dist'),
        //生成dist时，自动清理上次打包遗留文件
        clean: true,
        //配置资源模块打包后的路径，[contenthash]表示根据文件内容生成一个哈希字符串，[ext]表示使用原扩展名
        assetModuleFilename:'images/[contenthash][ext]'
    },
  	...
}
```

### 1.10.2 防止重复

> 相较于上一种，依旧使用entry进行配置，但是能够将共享代码提取出单独的bundle.js

#### 1.10.2.1 方案1

```json
entry:{
  index:{
    import:'./src/index.js',
    dependOn:'shared'
  }, 
  test:{
    import:'./src/test.js',
    dependOn:'shared'
  },
  //当都存在lodash模块时，抽离出额外的文件命名为shared.bundle.js
  shared: 'lodash'
},
```

最终导出文件：

- index.bundle.js
- test.bundle.js
- Shared.bundle.js

#### 1.10.2.2 方案2

```json
module.exports ={
  entry:{
        index:'./src/index.js',
        test:'./src/test.js'
    },
  ...
  //优化配置
    optimization:{
        //实现代码分割，并且把公共资源文件分到一个js中
        splitChunks:{
            chunks:'all'
        }
    }
}
```

### 1.10.3 动态导入

> 使用import进行动态导入
>
> 使用import导入会返回一个promise

**应用场景1：懒加载**

```json
//concat.js
function concat(){
    return ['a','b'].join('-');
}

export {concat} 

//test.js动态导入concat.js,那么在事件未出发之前，将不会进行concat.js相关资源加载
const button = document.createElement('button');
button.textContent='点击我加载'
button.addEventListener('click',()=>{
    // /*webpackChunkName: 'concat' */为打包出来后的资源文件命名，下面例子将会打包出concat.bundle.js
    import(/*webpackChunkName: 'concat' */'./concat.js').then(({concat})=>{
        console.log(concat());
    })
})
document.body.appendChild(button)
```

**应用场景2：预获取/预处理模块**

> 在应用场景1中，需要点击按钮才能加载相关资源
>
> 例如，最终会在首页html又一个link：<link rel="prefetch" as="script" href="http://192.168.0.106:8080/concat.bundle.js">
>
> rel=“prefetch” ，则是首页加载完后，利用网页空闲时间进行的资源下载

```json
//webpackPrefetch
button.addEventListener('click',()=>{
    // webpackPrefetch:true,设置资源预处理
    import(/*webpackChunkName: 'concat',webpackPrefetch:true */'./concat.js').then(({concat})=>{
        console.log(concat());
    })
})
//webpackPreload跟懒加载方式类似，点击之后进行资源加载
button.addEventListener('click',()=>{
    import(/*webpackChunkName: 'concat',webpackPreload:true */'./concat.js').then(({concat})=>{
        console.log(concat());
    })
})
```

## 1.11 缓存

### 1.11.1 业务代码

> 浏览器缓存机制：会根据重名文件名中缓存，因此，我们需要将文件名与文件内容相关,利用[contenthash]

```json
//出口配置
    output:{
        /**
         * 出口文件名
         * @description:单文件输出可以例如：filename:'bundle.js',
         * @description:多个入口文件配置可以使用[name]获取对应的入口文件名
         * @description:[contenthash]:根据内容生成哈希字符串，作用：可以根据内容变化生成新的文件名，防止浏览器缓存（浏览器根据文件资源的名字命中缓存）
         */
        filename:'[name].[contenthash].js',
        //存放路径，必须填写绝对路径,resolve(当前文件路径，拼接路径)最终形成绝对路径
        path:path.resolve(__dirname,'./dist'),
        //生成dist时，自动清理上次打包遗留文件
        clean: true,
        //配置资源模块打包后的路径，[contenthash]表示根据文件内容生成一个哈希字符串，[ext]表示使用原扩展名
        assetModuleFilename:'images/[contenthash][ext]'
    },
```

### 1.11.2 公用代码

> 根据浏览器缓存机制，将公用代码（较少变化的代码）单独打包，尽可能让浏览器名中缓存

```json
//优化配置
    optimization:{
        minimizer:[
            new CssMinimizerPlugin()
        ],
        //实现代码分割，并且把公共资源文件分到一个js中
        // splitChunks:{
        //     chunks:'all'
        // }
        splitChunks:{
            //缓存组，将test匹配到的公共库内的代码进行单独的打包，名为vendors
            cacheGroups:{
                vendor:{
                    test:/[\\/]node_modules[\\/]/,
                    name:'vendors',
                    chunks:'all'
                }
            }
        }
    }
```

## 1.12 js代码压缩

- 安装插件`npm install terser-webpack-plugin -D`

- 修改配置webpack.config.js

  ```json
  const TerserPlugin = require('terser-webpack-plugin')
  module.exports = (env)=>{
      console.log(env);
      return {
         ...
           //优化配置
          optimization:{
              minimizer:[
                  new CssMinimizerPlugin(),
                  new TerserPlugin()
              ],
              ...
          }
            
      }
  }
  ```

  

## 1.13 拆分开发环境和生产环境的配置

### 1.13.1 环境变量env

- 将整个配置module.exports的对象变成函数返回对象

  ```json
  module.exports = (env)=>{
      console.log(env);
      return {
         ...
          mode:'development',   
            
      }
  }
  ```

- 通过命令行运行传参，例如输入`npx webpack --env production --env param1`

  ```json
  module.exports = (env)=>{
      /*
      此处将会打印下列信息
      {
        WEBPACK_BUNDLE: true,
        WEBPACK_BUILD: true,
        production: true,
        test: true
  		}
      */
      console.log(env);
      return {
         ...
        //根据env接收的参数变更配置
          mode:env.production?'production':'development',   
            
      }
  }

### 1.13.2 配置的拆分

- 新建一个文件例如：`webpack.config.dev.js`，将开发环境配置好
- 运行自定义配置文件打包：`npx webpack -c webpack.config.dev.js`

### 1.13.3 npm脚本启动命令优化

- 在package.json中配置script

  ``` json
  {
    "script":{
      "start": "webpack -c webpack.config.dev.js",//npm start即可启动
      "build": "webpack -c webpack.config.prod.js"//npm build即可启动
    }
  }
  ```

### 1.13.4 合并配置文件

- 新建公共配置文件例如`webpack.config.common.js`，将公共配置挪到文件内，删除开发和生产配置文件公共配置部分

- 安装插件`npm install webpack-merge -D`

- 新建`webpack.config.js`，内容如下：

  ```javascript
  const {merge} = require('webpack-merge')
  
  const commonConfig = require('./webpack.config.common')
  const productionConfig = require('./webpack.config.prod')
  const developmentConfig = require('./webpack.config.dev')
  
  module.exports = (env) =>{
      switch(true){
          case env.development:
              return merge(commonConfig,developmentConfig)
          case env.production:
              return merge(commonConfig,productionConfig)
          default:
              return new Error('no matching configuration was found')
      }
  }
  ```

- Package.json启动命令如下

  ```json
  "scripts": {
      "start": "webpack -c ./config/webpack.config.js --env development",
      "build": "webpack -c ./config/webpack.config.js --env production"
    },
  ```

# 2 webpack高级篇

## 2.1 插件：_source-map

- 在开发环境中（mode = production），默认设置`devtool:eval`，每个module会封装到eval中包裹执行，并在末尾追加注释（@sourceURL）
- `source-map`：生成一个SourceMap文件，其他保持eval功能
- `hidden-source-map`：和source-map一样，但是不会在bundle末尾追加注释，即无法在浏览器锁定代码行数
- `inline-source-map`：可以锁定代码行数，和source-map唯一的区别即不会单独生成sourcemap文件
- `eval-source-map`：每个module会封装到eval中包裹执行，不会生成sourcemap文件，而是在末尾生成dataurl形式的sourcemap
- `cheap-source-map`：可以减少main.map.js文件大小，即mapping中只显示代码行数，不显示列数
- `cheap-module-source-map`：建议开发环境下使用，单独生成sourcemap文件，针对babel解析类后不会造成代码行数差异，正确锁定源码位置

## 2.2 热更新：devServer

```json
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
module.exports={
    mode:'development',

    entry:'./app.js',

    output:{
        clean:true,
    },
    devServer: {
        static:path.resolve(__dirname,'./dist'),
        //是否在传输过程中压缩代码
        compress:true,
        //配置端口号
        port:2000,
        //头部信息
        headers:{
            'X-Access-Token':'test'
        },
        //配置代理
        proxy:{
            '/api':'http://localhost:9000'
        },
        //是否开启https，需要配置安全证书，也可以使用http2自带安全证书
        https:false,
        //找不到页面的时候返回首页
        historyApiFallback: true,
        //配置后局域网内的可以通过地址访问服务
        host: '0.0.0.0',
        //模块热替换，默认为true
        hot:true,
        //热更新，自动编译刷新，默认值为true
        liveReload:true
    },
    
    plugins:[
        new HtmlWebpackPlugin()
    ]

}
```

## 2.3 Githooks：代码检测

- git初始化：`git init`
- 进入hooks目录：`cd git/hooks`，列出当前所有文件`ls -la`
- 可以观察到很多.sample文件，即git的钩子
- 例如在提交前检查，对应的是`pre-commit.sample`,我们需要创建一个文件`touch pre-commit`,再列出所有文件可观察到我们新建的文件
- 编辑新建的文件`vim pre-commit`,可在当中写命令行，在提交前会执行，例如利用eslint代码检测`npx eslint ./src`
- 输入内容完成后按`esc`退出,再按冒号输入wq退出
- 给新建文件加可执行权限`chmod +x ./pre-commit  `

### 2.3.1 husky：进行配置git hooks

- https://www.npmjs.com/package/husky

## 2.4 模块导入别名

- 可为指定路径命名一个别名代替

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
module.exports={
    mode:'development',

    entry:'./app.js',

    output:{
        clean:true,
        publicPath:'/'
    },
    resolve:{
        //引入路径别名
        alias:{
            '@':path.resolve(__dirname,'./tools')
        },
        //请求模块的扩展名按照该顺序查找
        extensions:['.json','.js','.vue'],
    },
    plugins:[
        new HtmlWebpackPlugin()
    ]

}
```

## 2.5 外部扩展-CDN加速地址

- https://www.bootcdn.cn/

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
module.exports={
    mode:'development',

    entry:'./app.js',

    output:{
        clean:true,
    },
    //以script标签引入
    externalsType:'script',
    //外部扩展
    externals:{
        jquery: [
            'https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js',
            '$'
        ]
    },
    plugins:[
        new HtmlWebpackPlugin()
    ]

}
```

