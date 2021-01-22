原文链接：[https://segmentfault.com/a/1190000018633603?utm\_medium=hao.caibaojian.com](https://segmentfault.com/a/1190000018633603?utm_medium=hao.caibaojian.com)

从零开始学习 React 高阶组件
=================

01、介绍
-----

*   React 高阶组件也叫做 React HOC(High Order Component), 它是react中的高级技术, 用来重用组件逻辑。
*   但高阶组件本身并不是React API。它只是一种模式，这种模式是由react自身的组合性质必然产生的。
*   那么在学习高阶组件之前有一个概念我们必须清楚，就是高阶函数。

02、高阶函数
-------

*   概念：**高阶函数是一个函数，它接收函数作为参数或将函数作为输出返回**
*   举个栗子:
    
    *   接收函数作为参数
        
            function a(x) {
              x();
            }
            function b() {
              alert('hello');
            }
            
            a(b);
        
    *   将函数作为输出返回
        
            function a() {
              function b() {
                alert('hello');
              }
              return b;
            }
            
            a()();
        
*   以上函数a就是一个高阶函数, 用法非常简单, 那么实际开发中又有哪些是高阶函数呢？
    
    *   Array 的 map 、reduce 、filter 等方法
    *   Object 的 keys 、values 等方法

03、高阶组件
-------

*   概念：**高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件**
*   举个栗子：
    
        // WrappedComponent 就是传入的包装组件
        function withHoc(WrappedComponent) {
          return class extends Component {
            render () {
              return (
                <WrappedComponent />
              )
            }
          }
        }
    
*   withHoc 函数就是一个高阶组件。那么高阶组件到底有什么神奇的魔力，值得我们为之着迷？
*   **开发组件时，我们会遇到相同的功能，使用高阶组件则能减少重复代码**

04、高阶组件实训1
----------

*   目的: 定义高阶组件
*   组件 Login -- 登陆页面
    
        // 受控组件
        class Login extends Component {
          state = {
            username: '',
            password: ''
          }
          
          onUsernameChange = (e) => {
            this.setState({username: e.target.value});
          }
          
          onPasswordChange = (e) => {
            this.setState({password: e.target.value});
          }
          
          login = (e) => {
            // 禁止默认事件
            e.preventDefault();
            // 收集表单数据
            const { username, password } = this.state;
            alert(`用户名: ${username}, 密码: ${password}`);
          }
          
          render () {
            const { username, password } = this.state;
            return (
              <div>
                <h2>登陆</h2>
                <form onSubmit={this.login}>
                  用户名: <input type="text" name="username" value={username} onChange={this.onUsernameChange}/> <br/>
                  密码: <input type="password" name="password" value={password} onChange={this.onPasswordChange}/> <br/>
                  <input type="submit" value="登陆"/>
                </form>
              </div>
            )
          }
        }
    
*   组件 Register -- 注册页面
    
        // 受控组件
        class Register extends Component {
          state = {
            username: '',
            password: '',
            rePassword: ''
          }
          
          onUsernameChange = (e) => {
            this.setState({username: e.target.value});
          }
          
          onPasswordChange = (e) => {
            this.setState({password: e.target.value});
          }
          
          onRePasswordChange = (e) => {
            this.setState({rePassword: e.target.value});
          }
          
          register = (e) => {
            // 禁止默认事件
            e.preventDefault();
            // 收集表单数据
            const { username, password, rePassword } = this.state;
            alert(`用户名: ${username}, 密码: ${password}, 确认密码: ${rePassword}`);
          }
          
          render () {
            const { username, password, rePassword } = this.state;
            return (
              <div>
                <h2>注册</h2>
                <form onSubmit={this.register}>
                  用户名: <input type="text" name="username" value={username} onChange={this.onUsernameChange}/> <br/>
                  密码: <input type="password" name="password" value={password} onChange={this.onPasswordChange}/> <br/>
                  确认密码: <input type="password" name="rePassword" value={rePassword} onChange={this.onRePasswordChange}/> <br/>
                  <input type="submit" value="注册"/>
                </form>
              </div>
            )
          }
        }
    
*   页面效果  
    ![](https://segmentfault.com/img/remote/1460000018633606?w=588&h=404)
*   我们发现里面重复逻辑实在太多了，尤其是 onXxxChange 函数出现太多，我们先优化一下。
    
        // 我们以 Register 组件为例来看
        class Register extends Component {
          state = {
            username: '',
            password: '',
            rePassword: ''
          }
          // 最终修改状态数据的函数
          onChange = (stateName, stateValue) => {
            this.setState({[stateName]: stateValue});
          }
          // 高阶函数 --> 这样后面就能一直复用当前函数，而不用重新创建了~
          composeChange = (name) => {
            return (e) => this.onChange(name, e.target.value);
          }
          // 统一所有提交表单函数名
          handleSubmit = (e) => {
            e.preventDefault();
            const { username, password, rePassword } = this.state;
            alert(`用户名: ${username}, 密码: ${password}, 确认密码: ${rePassword}`);
          }
          
          render () {
            const { username, password, rePassword } = this.state;
            return (
              <div>
                <h2>注册</h2>
                <form onSubmit={this.handleSubmit}>
                  用户名: <input type="text" name="username" value={username} onChange={this.composeChange('username')}/> <br/>
                  密码: <input type="password" name="password" value={password} onChange={this.composeChange('password')}/> <br/>
                  确认密码: <input type="password" name="rePassword" value={rePassword} onChange={this.composeChange('rePassword')}/> <br/>
                  <input type="submit" value="注册"/>
                </form>
              </div>
            )
          }
        }
    
*   现在两个页面都有 onChange 、 composeChange 、handleSubmit 函数和相关的状态，我们接下来提取，封装成**高阶组件**！
    
        // 高阶组件 withHoc
        export default function withHoc(WrappedComponent) {
          return class extends Component {
            state = {
              username: '',
              password: '',
              rePassword: ''
            }
          
            onChange = (stateName, stateValue) => {
              this.setState({[stateName]: stateValue});
            }
          
            composeChange = (name) => {
              return (e) => this.onChange(name, e.target.value);
            }
            
            handleSubmit = (e) => {
              e.preventDefault();
              const { username, password, rePassword } = this.state;
              if (rePassword) {
                alert(`用户名: ${username}, 密码: ${password}, 确认密码: ${rePassword}`);
              } else {
                alert(`用户名: ${username}, 密码: ${password}`);
              }
            }
            
            render () {
              // 抽取方法
              const mapMethodToProps = {
                composeChange: this.composeChange,
                handleSubmit: this.handleSubmit,
              }
              // 将状态数据和操作的方法以 props 的方式传入的包装组件中
              return (
                <div>
                  {/*提取公共头部*/}
                  <h2>xxx</h2>
                  <WrappedComponent {...this.state} {...mapMethodToProps}/>
                </div>
              )
            }
          }
        }
        
        // 组件 Register
        class Register extends Component {
          render () {
            const { handleSubmit, composeChange, username, password, rePassword } = this.props;
            return (
              <form onSubmit={handleSubmit}>
                用户名: <input type="text" name="username" value={username} onChange={composeChange('username')}/> <br/>
                密码: <input type="password" name="password" value={password} onChange={composeChange('password')}/> <br/>
                确认密码: <input type="password" name="rePassword" value={rePassword} onChange={composeChange('rePassword')}/> <br/>
                <input type="submit" value="注册"/>
              </form>
            )
          }
        }
        // 向外暴露的是高阶组件的返回值~包装了 Register 组件返回了一个新组件
        export default withHoc(Register);
    
*   现在我们提取了公共方法、状态等数据, 封装了一个基本的高阶组件。 但是还有很多需要问题需要解决，现在开始行动~

05、高阶组件实训2
----------

*   目的: 向高阶组件中传参
*   修改高阶组件
    
        // 再次包裹了一层高阶函数, 这个高阶函数执行后返回值才是高阶组件
        // 通过这种方式, 高阶组件内部就能获取参数了~
        export default (title) => (WrappedComponent) => {
          return class Form extends Component {
            ...重复代码省略...
            
            render () {
              const mapMethodToProps = {
                composeChange: this.composeChange,
                handleSubmit: this.handleSubmit,
              }
              return (
                <div>
                  {/*获取到参数值就能正常显示了~*/}
                  <h2>{title}</h2>
                  <WrappedComponent {...this.state} {...mapMethodToProps}/>
                </div>
              )
            }
          }
        }
    
*   在 Login / Register 组件中使用
    
    *   export default withHoc('登陆')(Login);
    *   export default withHoc('注册')(Register);

06、高阶组件实训3
----------

*   目的: 获取父组件传递的 props
*   修改 App 组件
    
        class App extends Component {
          render() {
            return (
              <div>
                {/*父组件向子组件传递属性*/}
                <Login name="jack" age={18}/>
                <Register />
              </div>
            );
          }
        }
    
*   修改高阶组件
    
        export default (title) => (WrappedComponent) => {
          return class Form extends Component {
            ...重复代码省略...
            
            render () {
              const mapMethodToProps = {
                composeChange: this.composeChange,
                handleSubmit: this.handleSubmit,
              }
              return (
                <div>
                  {/*获取到参数值就能正常显示了~*/}
                  <h2>{title}</h2>
                  {/* 将当前组件接受到的props传给包装组件~*/}
                  <WrappedComponent {...this.props} {...this.state} {...mapMethodToProps}/>
                </div>
              )
            }
          }
        }
    
*   Login 组件中使用
    
         class Login extends Component {
           render () {
             const { handleSubmit, composeChange, username, password, name, age } = this.props;
             return (
               <div>
                 <p>你的名字: {name}</p>
                 <p>你的年龄: {age}</p>
                 <form onSubmit={handleSubmit}>
                   用户名: <input type="text" name="username" value={username} onChange={composeChange('username')}/> <br/>
                   密码: <input type="password" name="password" value={password} onChange={composeChange('password')}/> <br/>
                   <input type="submit" value="登陆"/>
                 </form>
               </div>
             )
           }
         }
    

07、高阶组件实训4
----------

*   目的: 修改在 React-devtool 中高阶组件名称，方便调试
*   修改高阶组件
    
        export default (title) => (WrappedComponent) => {
          return class Form extends Component {
            // 定义静态方法，修改组件在调试工具中显示的名称
            static displayName = `Form(${getDisplayName(WrappedComponent)})`
            
            ...省略重复代码...
          }
        }
        // 获取包装组件的displayName的方法
        function getDisplayName(WrappedComponent) {
          return WrappedComponent.displayName || WrappedComponent.name || 'Component';
        }
    
*   修改之前名称

![](https://segmentfault.com/img/remote/1460000018633607?w=298&h=127)

*   修改之后名称

![](https://segmentfault.com/img/remote/1460000018633608?w=389&h=133)

08、使用装饰器
--------

*   目的: 简化使用高阶组件
*   下载包
    
    *   npm i react-app-rewired customize-cra @babel/plugin-proposal-decorators -D
*   在项目根目录配置 config-overrides.js
    
        const { override, addBabelPlugins } = require('customize-cra');
        // 修改 create-react-app 的 webpack 的配置
        module.exports = override(
          // 添加 babel 插件
          addBabelPlugins(
            [
              "@babel/plugin-proposal-decorators",
              {
                "legacy": true
              }
            ]
          )
        )
    
*   修改 package.json 的 scripts
    
        // 将 react-scripts 修改为 react-app-rewired
        "scripts": {
          "start": "react-app-rewired start",
          "build": "react-app-rewired build",
          "test": "react-app-rewired test"
        },
    
*   以上就是使用 decorator 的配置，修改完后就能使用了~
*   修改 Login 组件
    
        @withHoc('登陆')
        class Login extends Component {
          ...省略重复代码...
        }
        export default Login;
    
*   修改 Register 组件
    
        @withHoc('注册')
        class Register extends Component {
          ...省略重复代码...
        }
        export default Register;
    
*   react-app-rewired customize-cra 是 create-react-app 2.0以上专门用来修改 webpack 的配置
*   decorator 还能做很多事，感兴趣朋友可以看看 [阮一峰ES6教程](http://es6.ruanyifeng.com/#docs/decorator) 了解更多

> 重复代码永远是我们需要考虑处理的代码，所以我们有模块化、组件化、工具类函数等等，  
> 在 React 中再次引入了一个高阶组件的概念，都是为了去除掉万恶的重复代码，让我们代码变得更加精简。  
> 本篇文章所有源码都放在了 [git仓库](https://github.com/xxpromise/React-HOC)，如果它对你有帮助的话，欢迎点 star ~~