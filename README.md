# react

## 虚拟DOM和真实DOM
* 虚拟DOM是一个对象，只有10多个属性（轻，方便操作，节能）
* 真实DOM也是一个对象，有很多属性（更重）


## jsx语法
* className 给虚拟DOM添加类名

```jsx
let vDom2 = (
  <ul>
    {arr.reduce((pre, next) => {
      pre.push(<li key={next}>{next}</li>)
      return pre
    }, [])}
  </ul>
)
ReactDOM.render(vDom2, document.getElementById('app'))
```

* style样式用对象赋值

```jsx
let vDom3 = <h3 style={{backgroundColor:'pink'}}></h3>
```

## 组件


### 函数组件
> 简单组件

* 组件的函数首字母必须大写
* ReactDOM.render渲染组件时，必须首字母大写

### Fragment
空的占位组件

```jsx
const Fragment = React.Fragment
class Person2 extends React.Component {
  render(){
    return (
      <Fragment>
          <button>点击</button>
          <h2>点击显示/隐藏</h2>
      </Fragment>
    )
  }
}
```

### props

* 组件的属性名和属性值组成的对象会作为实参props传递到函数组件中


### propTypes
类型检测

* string 属性类型
* isRequired 必传属性
* defaultProps 默认属性

```jsx
function Person(props){
  console.log(props)
  return <h2>我是函数组件</h2>
}

Person.defaultProps = { // 默认属性
  name: '默认nme'
}

Person.propTypes = {
  id: PropTypes.string.isRequired, // 接受的id必须为字符串
}

ReactDOM.render(<Person id='a' name='b'/>, document.getElementById('app'))
```


### 类组件
> 复杂组件

```jsx
class Person1 extends React.Component {
  constructor(){
    super() // 继承父类的constructor
    console.log(props)
  }
  render(){
    return <h2>我是es6类组件</h2>
  }
}

ReactDOM.render(<Person1 id='a' name='b'/>, document.getElementById('app'))
```

* 通过扩展运算符传值
```jsx
let obj = {
  name: '',
  age: '',
  sex: ''
}
ReactDOM.render(<Person1 {...obj}/>, document.getElementById('app'))
```

### state
定义组件时自行创建的,类组件才有state,函数组件没有

* setState 修改state中的值,并会触发render函数
```jsx
class Person1 extends React.Component {
  constructor(props){
    super()
    this.state = {
      count: 0
    }
  }

  handleClick(){
    // 若直接修改this.state无法触发render,则视图不会更新
    let {count} = this.state
    count++
    this.setState({count})
  }

  render(){ // 必须通过setState函数改变state的数据,才会再次触发render
    console.log(this) // this是Person的实例化对象
    // 当h2标签被点击时,this指向window,因此需要改变this指向,才能在点击时获取到数据
    return <h2 onClick={this.handleClick.bind(this)}>我是es6类组件，{this.state.count}</h2>
  }
}
ReactDOM.render(<Person1 />, document.getElementById('app'))
```



### ref
> 给原生dom打上标记,方便获取

* 字符串形式,通过this.refs.xxx获取
* 箭头函数形式,直接this.xxx获取
* creatRef创建,通过this.xxx.current

```jsx
class Person3 extends React.Component {
  handleClick(e){
    e.preventDefault(); // 阻止默认事件
    this.sex = React.createRef()
    consolee.log(this.sex)
    console.log(this.refs.username)
    console.log(this.psd)
  }
  render(){
    return (
      <form action="/" method='get'>
        用户:<input type='text' ref='username'/><br/>
        密码:<input type='text' ref={psd=>this.psd=psd}/><br/>
        性别:<input type='text' ref={this.sex}/><br/>
        <input type='submit' onClick={this.handleClick.bind(this)}/>
      </form>
    )
  }
}
```

> 在点击时才获取到表单的数据,非受控表单

### 受控表单
* 任何时刻都能拿到input中的数据,完全受控表单

```jsx
class Person3 extends React.Component {
  constructor(){
    super()
    this.state = {
      username: 'sads',
      psd: ''
    }
  }
  handleChange(e){
    this.setState({
      username: e.target.value
    })
  }
  render(){
    const {username,psd} = this.state
    return (
      <form action="/" method='get'>
        用户:<input type='text' value={username} onChange={this.handleChange.bind(this)}/><br/>
      </form>
    )
  }
}
```


### this指向
实例化对象下函数属性的this指向

* 使用=给a赋值函数的形式，就会给xxx的实例化对象下添加一个函数a属性，并且由于箭头函数没有this，this指向实例化对象
```jsx
class xxx{
  a = ()=>{}
}
```

### 组件化

* 父子通信，属性传值
* 修改数据，通过调用父组件传入的函数


## 生命周期函数

### 生存期

* componentWillMount 挂载前
* render 挂载
* componentDidMount 挂载后

### state改变

* shouldComponentUpdate 允许更新
* componentWillUpdate 更新前
* render 更新
* compoentDidUpdate 更新后

### props改变

* componentWillReceiveProps props改变后
* componentWillUnmount 销毁前


## setState

### 特性
* 在react的事件处理函数、生命周期函数中
  * setState是异步的
  * 同一个函数中，会把多个setState合并成一个  
  * 在第二个参数中，可以传入回调函数，获取到更新后（render执行后）的state的数据
* 在原生的DOM事件处理函数、定时器的回调函数、promise的then回调中
  * setState是同步的
  * setState不会合并，所有获取到的state数据都是最新的

* 传递的对象
```js
state = { xxx: 0 }
// 当使用this.state.xxx获取时，得到到的是最原始的xxx值
this.setState({
  xxx: this.state.xxx++ // 0+1=1
})
this.setState({
  xxx: this.state.xxx++ // 0+1=1
})
```
* 传递的函数
```js
state = { xxx: 0 }
// 在函数中的state参数获取到的是最新的xxx值
this.setState(state=>{
  return {
    xxx: state.xxx++ // 0+1=1
  }
})
this.setState(state=>{
  return {
    xxx: state.xxx++ // 1+1=2
  }
})
```
* 传回调函数
```js
state = { xxx: 0 }
this.setState({
  xxx: this.state.xxx++ // 0+1=1
},()=>{
  // 执行render之后执行
  console.log(this.state.xxx) // 1
})
```

* 在原生的DOM事件处理函数、定时器的回调函数、promise的then回调中
```js
state = { xxx: 0 }
dom.onclick = ()=>{
  this.setState({
    xxx: this.state.xxx++ // 0+1=1
  })
  this.setState(state=>{
    return {
      xxx: state.xxx++ // 1+1=2
    }
  })
  this.setState({
    xxx: this.state.xxx++ // 2+1=3
  })
}
// 后执行
setTumeout(()=>{
  this.setState({
    xxx: this.state.xxx++ // 4+1=5
  })
},0)
// Promise的then回调先执行
Promise.resolve().the(()=>{
  this.setState({
    xxx: this.state.xxx++ // 3+1=4
  })
})

```


### key

没有使用key值时，循环的元素中有子元素时，删除某个元素，子元素不会变化



## 通信


### 父传子
* 属性


### 子传父
* 父组件通过属性传递函数给子组件，子组件调用父组件的函数并传递数据

```jsx
class Son1 extends Component {
  state = {
    msg: "我是son组件的数据"
  }
  sendMsg = ()=>{
    this.props.receiveMsg(this.state.msg)
  }
  render() {
    return (
      <div>子组件
        <button onClick={this.sendMsg}>点击发生数据</button>
      </div>
    )
  }
}
class App extends Component {
  receiveMsg = (msg)=>{
    console.log(msg)
  }
  render(){
    return (
      <div>
        <div>父组件</div>
        <Son1 receiveMsg={this.receiveMsg}/>
      </div>
    )
  }
}
```

### 兄弟组件
* 使用PubSub
```jsx
class Son1 extends Component {
  state = {
    msg: "我是son1组件的数据"
  }
  sendMsg = ()=>{
    // 触发receiveSon1Msg事件
    PubSub.publish('receiveSon1Msg',this.state.msg)
  }
  render() {
    return (
      <div>
        子组件son1
        <button onClick={this.sendMsg}>点击发生数据</button>
      </div>
    )
  }
}
class Son2 extends Component {
  componentDidMount(){
    // 订阅事件，等待son1触发
    PubSub.subscribe('receiveSon1Msg',function(type,msg){
      // 当触发receiveSon1Msg事件时，执行回调函数
      console.log(type,msg)
    })
  }
  render() {
    return (
      <div>子组件son2</div>
    )
  }
}
class App extends Component {
  render(){
    return (
      <div>
        <div>父组件</div>
        <Son1/>
        <Son2/>
      </div>
    )
  }
}
```

## 动画

### CSSTransition
* timeout 毫秒数，动画执行时间
* in 布尔值，动画执行
* appear 布尔值，初次执行动画
* classNames 更改enter的类名

* onEnter 动画入场时，触发函数
* onEntering 动画入场中，触发函数
* onEntered 动画结束后，触发函数


#### 类名
```css
.enter {}
.enter-active {}
.enter-done
.exit {}
.exit-active {}
.exit-done {}
```

### TransitionGroup
一组元素动画，页面进入时入场动画，元素消除时离场动画




## 效能


### 更新组件
```jsx
// 判断数据是否更改决定视图是否更新
shouldComponentUpdate(nextProps){
  // nextProps 是父组件传递过来的更新后的props属性
  if( nextProps.xxx === this.props.xxx ) return false
  else return true
}
```

### PureComponent
纯洁组件，内部对父组件传递的数据做过上面的判断，数据不更新时不更新视图



## 脚手架



## 路由
> 是一个映射关系

* 后台路由：路径和 **回调函数** 构成了映射关系
  * 一定会发起网络请求
  * 一定会刷新页面
* 前台路由：路径和 **组件** 构成映射关系
  * 实现原理基于H5的history.pushState 实现的单页面应用SPA
  * 一定不会发起网路请求
  * 一定不会刷新页面


### 路由组件
只要是路由组件，就会带有：
* history 储存一个跳转路径的方法
* location 存放数据
* match 解析路径得到的数据


```jsx
import { BrowserRouter as Router, NavLink, Route, Switch, Link, Redirect} from 'react-router-dom'
```

### BrowserRouter
想要使用react-router-dom，必须使用BrowserRouter降组件包起来


### NavLink
* NavLink生成的a标签会添加类名active，Link则没有

#### to属性
匹配规则：包含匹配/模糊匹配，例如/homo/user会匹配到/ /home /home/user三个路由

```jsx
<NavLink to='/home'></NavLink>
<NavLink to={{pathname: '/home', hash: 'xxx', search: '?xxx=xxx'}}></NavLink>
```

* 通过to属性传递的数据都存放在this.props.location中
* 动态路由的id值在this.props.match.params中


#### js跳转
使用js实现to的路由跳转

* push 在原来的历史记录中 叠加
```js
/*
* /a/b/1
* /a/b/2
* /a/b/3
* 原路返回
*/
this.props.history.push({
  pathname: 'xxx',
  state: {}
})
```

* replace 替换 原来的历史记录
```js
/*
* /a/b/1 -/a/b/2 - a/b/3
* 返回 /a/b
*/
this.props.history.replace({
  pathname: 'xxx',
  state: {}
})
```

* goForwoard 前进，从历史记录中前进
```js
this.props.history.goForwoard
```

* goBack 后退，从历史记录中后退
```js
this.props.history.goBack
```

* go(n) 前进n次
```js
this.props.history.go()
```


#### exact
精确匹配，只匹配到对应的路由


#### activeClassName
* navlink生成的a标签会添加类名active
* activeClassName可以更改这个类名

#### state
额外的数据，实现与路由组件的通信


### Route
路由定向

### Switch
只渲染第一个满足条件的组件

### Redirect
重定向，显示默认组件

 
```jsx
<NavLink to={{pathname: '/home', hash: 'xxx', search: '?xxx=xxx', state: {msg: '数据'} }}></NavLink>
<NavLink to='/user'></NavLink>
<Switch>
  <Route path='/home' component={Home}></Route>
  <Route path='/user' component={User}></Route>
  <Redirect to='/home'></Redirect>
</Switch>
```


## 补充

* dangerouslySetInnerHTML 解析html 

### axios

```js
// 设置axios响应信息的数据格式
axios.interceptors.response.use(res=>res.data)
// 设置默认地址
axios.defaults.baseURL=''
```



## redux
数据管理库

* 创建仓库store，存放数据

```js
// store.js
import {createStore} from 'redux'
import reducer from './reducer'
export defalut createStore(reducer)
```

* 初始化仓库store中的数据state，以及定义处理仓库数据的action
  * 通过actions-type中定义的常量选择处理方式
```js
// reducer.js
import { TYPE1, TYPE2 } from 'actions-type.js'
export defalut function (state='xxx', action){
  // 不能直接修改state，例如state=xxxx
  // return 返回的值就是新的state值
  switch(){
    case TYPE1:
      return state和action处理的结果1
    case  TYPE2:
      return state和action处理的结果2
    default:
      return 默认结果state
  }
}
```

* 动作，存储动作类型和数据的对象，修改仓库数据的函数

```js
// actions.js 
import { TYPE1, TYPE2 } from 'actions-type.js'
// 同步，返回对象
export const actionType1 = (data1) =>{
  return {
    type: TYPE1, //类型
    data: data1 // 数据
  }
}
export const actionType2 = (data2) =>{
  return {
    type: TYPE2,
    data: data2
  }
}
// 异步，需要使用插件thunk
```

* 类型检测，通过定义action的处理类型常量，在常量使用错误是报错方便定位
```js
// actions-type.js
export const TYPE1 = 'type1' // action类型1
export const TYPE2 = 'type2' // action类型2
```

* 页面数据实时更新
```jsx
// index.js
import store from 'redux/store'
store.subscribe(render)
render()
function render(){
  ReactDOM.render(<App store={store} />,获取的dom元素)
}
```

#### getStatee 获取数据
```js
this.props.store.getState()
```

#### dispatch 触发action，修改数据
* 调用dispatch函数，参数会在被reducer.js中的action接收
* 当异步更新时，需要使用thunk插件，调用dispath时传入回调函数，系统会在时间到执行回调函数，并将dispatch作为参数传入回调

* 直接在app.js中执行action操作
```js
// 同步
this.props.store.dispatch({type: '操作类型', data: '数据'})
// 异步
this.props.store.dispatch(dispatch => {
  setTimeout(()=>{
    dispatch({type: '操作类型', data: '数据'})
  })
})
```

* 在app引入action操作函数使用
```js
// app.js
import { actionType1, actionType2, asyncActionType3 } from 'redux/actions.js'
// 同步
this.props.store.dispatch(actionType1(data1))
this.props.store.dispatch(actionType2(data2))
// 异步
this.props.store.dispatch(asyncActionType3(data3))

```


### redux-thunk
异步修改action

```js
// store.js
// applyMiddleware 使用中间件
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'
import reducer from './reducer'
export defalut createStore(reducer, applyMiddleware(thunk))
```

* 异步的action处理函数

```js
// action.js
// 异步时，返回一个函数，系统默认传参dispatch
export const asyncActionType3 = (data3) =>{
  return dispath=>{
    // 异步操作
    setTimeout(()=>{
      // 调用同步 action类型1函数
      dispatch(actionType1(data3))
    },2000)
  }
}
```
  

### react-redux
* 直接在app.js中调用store中的数据和方法太过麻烦和复杂，导致代码太多
* 使用react-redux让app更好的和redux耦合，更方便使用数据和处理函数


* 使用react-redux的Provider组件，实时更新数据
```jsx
// index.js
import { Provider } from 'react-redux'
// 不需要使用store.subscribe监听数据改变来更新页面，Provider自动更新store为最新的数据
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>
,获取的dom元素)
```

* 使用react-redux的connect函数连接仓库
  * connect 把仓库的属性映射为App的props下的属性，以便直接以this.props.xxx调用仓库数据
```js
// app.js
import { actionType1, actionType2, asyncActionType3 } from 'redux/actions.js'
// 仓库store中的state映射为app的属性
function mapStateToProps(state){ 
  // state -- 仓库中的数据，即reducer中返回的数据
  return { // 返回一个对象
    变量名: state
  }
}
// 把dispatch映射为app的属性，可以理解为是那些action操作函数
function mapDispatchToprops(dispatch){
  return {
    actionType1: (data1)=>dispatch(actionType1(data1)),
    actionType2: (data1)=>dispatch(actionType2(data2)),
    asyncActionType3: (data1)=>dispatch(asyncActionType3(data3)),
  }
}
export defalut connect(mapStateToProps, mapDispatchToprops)(App)
```

* 简洁写法
```js
import { actionType1, actionType2, asyncActionType3 } from 'redux/actions.js'
export defalut connect(state=>{
  // 直接写成箭头函数返回数据对象
  return {
    变量名: state
  }
}), {
  // 可以直接写成对象形式，这时不需要使用dispatch，直接将方法付给参数对象的属性，可以理解为以传对象时，内部自动调用dispatch
  actionType1: actionType1,
  actionType2: actionType2,
  asyncActionType3: asyncActionType3
})(App)

// 简写
export defalut connect(state=>({变量名:state}),{actionType1,actionType2,asyncActionType3})(App)
```


### combineReducers
> 合并reducer暴露的多个数据，即通过combineReducers使reducer暴露多个数据

```js
// reducer.js
import {combineReducers} from 'redux'
function data1(state=1,action){
  return state
}
function data2(state=2,action){
  return state
}
// combineReducers 合并多个reducer
export defalut combineReducers({
  data1,
  data2,
})

// 暴露多个数据时，connect中接受到一个数据对象
export defalut connect(state=>{
  /*
    state = {
      data1,
      data2,
    }
  */
  return {
    data1: state.data1
  }
},{})(App)
```


## 高阶组件
* 高阶函数：接受函数作为参数的函数，或者return值为函数的函数；
* 同理，高阶组件既是接受组件A作为参数并返回组件B的函数


### 属性代理
> 在返回的组件B中定义组件A（基础组件）需要的属性，并将其传给组件A；例如组件A需要的value和onChange函数


```jsx
function BaseComponent(props){
  // {...props} 解构赋予属性
  return <input type="text" {...props} />
}
const highComponent = BaseComponent=>{
  return class extends React.Component {
    state = {
      value: ''
    }
    onchange = (e)=>{
      this.setState({value:e.target.value})
    }
    render (){
      const obj = {
        value: this.state.value,
        onChange: this.onchange
      }
      return <BaseComponent {...obj}/>
    }
  }
}
const Component1 =  highComponent(BaseComponent)
const Component2 =  highComponent(BaseComponent)
ReactDOM.render(
  <div>
    <Component1/>
    <Component2/>
  </div>
, document.getElementById('app'))
```


### 反向继承
继承基础组件

```jsx

class BaseComponent extends React.Component {
  state = {
    msg: '基础组件'
  }
  render(){
    return (
      <div>基础组件</div>
    )
  }
}
const reverseInherit = BaseComponent=>{
  return class extends BaseComponent {
    // 继承基础组件
    render (){
      // 可以获取到基础组件的数据
      console.log(this.state)
      // super指向父类的原型对象
      return super.render()
    }
  }
}
const Component =  reverseInherit(BaseComponent)
ReactDOM.render(<Component/>, document.getElementById('app'))
```

## hook特性

> 在react16.8版本之后才有的新特性

* hook特性使函数组件有state状态

### useState
> hook函数，使函数组件有state状态

* 传入默认值，返回两个参数
* 第一个数据
* 第二个修改数据的方法
```js
const [value,useValue] = useState('默认值')
```

* 受控表单



### useRef
> 生成一个标记对象

* 非受控表单

```jsx
const {useRef}= React
function App(){
  const inputRef = useRef('input')
  const divRef = useRef('div')
  return (
    <div>
      <input type='text' ref={inputRef}>
      <div ref={divRef}></div>
    </div>
  )
}
```

### useEffect()
> 模拟类组件的生命周期函数

* 回调函数一般执行副作用代码
  * 请求数据
  * 操作dom

* 初始化时，useEffect执行，此时相当于componentDidMount函数
* 数据变化后执行，此时相当于componentDidUpdate
* 返回函数可以模拟componentWillUnmomunt


```jsx
// 当我们需要在组件mount阶段，以及数据更新阶段都要执行副作用代码时，在类组件中，就需要调用componentDidMount和componentDidUpdate生命周期函数，而在函数组件中，只需要在useEffect中写副作用代码即可
function App(){
  useEffect(()=>{
    // 副作用代码
  })
  return (<div></div>)
}
```

* 模拟componentWillUnmomunt

```jsx
// 当useEffect返回一个函数时，初始化App组件，渲染dom元素之后，会执行useEffect；当useEffect内部代码修改数据时，会再次渲染dom元素，此时，会先执行上一次useEffect的返回值函数，然后再第二次执行useEffect；以此类推，除第一次执行useEffect之外的每次执行useEffect函数之前都会前执行useEffect的返回值函数
function App(){
  useEffect(()=>{
    return function(){
    }
  })
  return (<div></div>)
}
```

* 第二个参数

可以控制数据更新时是否执行useEffect；取决于useEffect函数中是否涉及到第二个参数，如果没有使用到第二个参数，则不会再次执行useEffect，反之有涉及则执行

```jsx
function App(){
  const [color,useColor] = useState('red')
  const [$,use$] = useState('阻止useEffect多次执行')
  useEffect(()=>{
    setInterval(()=>{
      const randomColor = '#'+Math.random().toString(16).slice(2,8)
      useColor(randomColor)
    },1000)
  },[$])
  return (
    <div style={{backgroundColor:color, padding:50}}>
    </div>
  )
}
```


### 自定义hook函数
管理组件的公共状态和公共逻辑


```jsx
// 使用既有hook函数生成数据，并返回将相同操作的数据修改函数和数据一同返回出去
const useRandomColor = (initColor)=>{
  const [color,useColor] = useState(initColor)
  const handleRandomColor= ()=>{
    const randomColor = '#'+Math.random().toString(16).slice(2,8)
    useColor(randomColor)
  }
  return [color,handleRandomColor]
}
```

### useReducer
> 模拟redux仓库

```jsx
const {useState,useEffect,useReducer} = React
// 定义初始数据
const number = 0
// 定义reducer函数，用于数据更新
const reducer = (state, action) =>{
  switch (action.type) {
    case 'add':
      return state+action.data
    case 'minus':
      return state-action.data
    default:
      return state
  }
}
function App(){
  /*
  1. 执行useReducer()，传入更新函数reducer和初始值number，返回仓库store，以及更新仓库数据的方法dispatch
  2. 再次更新数据时，不会再将初始值number传给仓库，而是直接获取仓库中保存的上一次的数据
  */
  const [store, dispatch] = useReducer(reducer, number)
  const add = ()=>{
    // dispatch -- 触发传入useReducer的reducer更新数据
    dispatch({type:'add', data: 1})
  }
  const minus = ()=>{
    dispatch({type:'minus', data: 1})
  }
  return (
    <div>
      number：{store}
      <button onClick={add}>增加</button>
      <button onClick={minus}>减少</button>
    </div>
  )
}
```