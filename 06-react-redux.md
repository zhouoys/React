# redux简介

## redux学习文档

1. 英文文档: https://redux.js.org/

2. 中文文档: http://www.redux.org.cn/

3. Github: https://github.com/reactjs/redux



##  redux是什么

1. redux是一个专门用于做**状态管理**的JS库(不是react插件库)。

2. 它可以用在react, angular, vue等项目中, 但基本与react配合使用。

3. 作用: 集中式管理react应用中多个组件**共享**的状态。



## 什么情况下需要使用redux

1. 某个组件的状态，需要让其他组件可以随时拿到（共享）。

2. 一个组件需要改变另一个组件的状态（通信）。

3. 总体原则：能不用就不用, 如果不用比较吃力才考虑使用。



# redux工作流程

![image-20210505221309816](image/image-20210505221309816.png)



![image-20210505230516722](image/image-20210505230516722.png)



# redux的三个核心概念

## action

1. 动作的对象

2. 包含2个属性

   + type：标识属性, 值为字符串, 唯一, 必要属性

   + data：数据属性, 值类型任意, 可选属性

3. 例子：{ type: 'ADD_STUDENT',data:{name: 'tom',age:18} }



## reducer

1. 用于初始化状态、加工状态。

2. 加工时，根据旧的state和action， 产生新的state的**纯函数**



## store

1. 将state、action、reducer联系在一起的对象

2. 如何得到此对象?

+ import {createStore} from 'redux'

+ import reducer from './reducers'

+ const store = createStore(reducer)

3. 此对象的功能?

+ getState(): 得到state

+ dispatch(action): 分发action, 触发reducer调用, 产生新的state

+ subscribe(listener): 注册监听, 当产生了新的state时, 自动调用





# redux-精简版

**原生react求和案例**

![image-20210505234621194](image/image-20210505234621194.png)

![image-20210505234902810](image/image-20210505234902810.png)

![image-20210505235438072](image/image-20210505235438072.png)





![image-20210505235755295](image/image-20210505235755295.png)

**Counter.jsx**

~~~jsx
import React, { Component } from 'react'
import './index.css'
export default class index extends Component {
    state = {
        counter:0
    }
    //正常加法
    normalAdd = ()=>{
        let {value} = this.selectRef;
        let {counter} = this.state;
        this.setState({
            counter:Number(counter)+Number(value)
        })
    }
    //正常减法
    normalSubtract = ()=>{
        let {value} = this.selectRef;
        let {counter} = this.state;
        this.setState({counter:Number(counter)-Number(value)})
    }
    //奇数加
    oddAdd = ()=>{
        let {value} = this.selectRef;
        let {counter} = this.state;
        if(counter % 2 !== 0){
            this.setState({
                counter:Number(counter)+Number(value)
            })
        }
    }
    //异步加
    asyncAdd = ()=>{
    setTimeout(()=>{
        let {value} = this.selectRef;
        let {counter} = this.state;
        this.setState({
            counter:Number(counter)+Number(value)
        })
    },1000)
    }
    render() {
        return (
            <div>
                <h1>当前求和为:{this.state.counter}</h1>
                <select className="select" ref={(e)=>{this.selectRef = e}}>
                    <option value="1" className="option">1</option>
                    <option value="2" className="option">2</option>
                    <option value="3" className="option">3</option>
                </select>&nbsp;&nbsp;
                <button onClick={this.normalAdd}>+</button>&nbsp;&nbsp;
                <button onClick={this.normalSubtract}>-</button>&nbsp;&nbsp;
                <button onClick={this.oddAdd}>求和奇数加</button>&nbsp;&nbsp;
                <button onClick={this.asyncAdd}>异步加</button>&nbsp;&nbsp;
            </div>
        )
    }
}

~~~



**采用精简的redux求和版本**

**安装redux**

~~~shell
yarn add redux
~~~

**创建文件**

~~~shell
src
 |--redux
   |--store.js
   |--counter_reducer.js

~~~



![image-20210506223432263](image/image-20210506223432263.png)



![image-20210506224641431](image/image-20210506224641431.png)



![image-20210506224826939](image/image-20210506224826939.png)



![image-20210506225326313](image/image-20210506225326313.png)





![image-20210506225630254](image/image-20210506225630254.png)





![image-20210506230319042](image/image-20210506230319042.png)



![image-20210506230417597](image/image-20210506230417597.png)



![image-20210506231056827](image/image-20210506231056827.png)





![image-20210506231239270](image/image-20210506231239270.png)



**Counter.jsx**

~~~jsx
import React, { Component } from 'react'
import './index.css'
import store from '../../redux/store.js'
export default class index extends Component {
    state = {carName:'奔驰CS6'}
    componentDidMount(){
    //检测redux中状态的变化，只要变化了，就调用render
    store.subscribe(()=>{
        this.setState({})
    })
    }
    //正常加法
    normalAdd = ()=>{
        let {value} = this.selectRef;
        store.dispatch({type:'normalAdd',data:value})
    }
    //正常减法
    normalSubtract = ()=>{
        let {value} = this.selectRef;
        store.dispatch({type:'normalSubtract',data:value})
    }
    //奇数加
    oddAdd = ()=>{
        let {value} = this.selectRef;
        let counter = store.getState();
        if(counter % 2 !== 0){
            store.dispatch({type:'normalAdd',data:value})
        }
    }
    //异步加
    asyncAdd = ()=>{
    setTimeout(()=>{
        let {value} = this.selectRef;
        store.dispatch({type:'normalAdd',data:value})
    },1000)
    }
    render() {
        return (
            <div>
                <h1>当前求和为:{store.getState()}</h1>
                <select className="select" ref={(e)=>{this.selectRef = e}}>
                    <option value="1" className="option">1</option>
                    <option value="2" className="option">2</option>
                    <option value="3" className="option">3</option>
                </select>&nbsp;&nbsp;
                <button onClick={this.normalAdd}>+</button>&nbsp;&nbsp;
                <button onClick={this.normalSubtract}>-</button>&nbsp;&nbsp;
                <button onClick={this.oddAdd}>求和奇数加</button>&nbsp;&nbsp;
                <button onClick={this.asyncAdd}>异步加</button>&nbsp;&nbsp;
            </div>
        )
    }
}
~~~

**counter_reducer.js**

~~~js
/**
 * 1.该文件是用于创建一个为Counter组件服务的reducer,reducer的本质是一个函数
 * 2.reducer函数会接到两个参数,分别为：之前的状态(preState),动作对象(action)
 */
const initState = 0;
function counterReducer(preState=initState,action){
    //从action对象中获取：type,data;
    const {type,data} = action;
    //根据type决定如何加工数据
    switch (type) {
        case 'normalAdd': //如果是加法
            return preState + data * 1;
        case 'normalSubtract': //如果是减法
            return preState - data * 1;
        default:
            return preState
    }
}
export default counterReducer
~~~

**store.js**

~~~javascript
/**
 * 该文件专门用于暴露一个store对象，整个应用只有一个store对象
 */
//引入createStore,专门用于创建整个redux中最和兴的store对象
import {createStore} from 'redux'
//引入为Counter组件服务的reducer
import counterReducer from './counter_reducer'
//暴露store
export default createStore(counterReducer);
~~~



**求和案例精简版总结**

~~~javascript
	(1).去除Count组件自身的状态
	(2).src下建立:
					-redux
						-store.js
						-count_reducer.js

	(3).store.js：
				1).引入redux中的createStore函数，创建一个store
				2).createStore调用时要传入一个为其服务的reducer
				3).记得暴露store对象

	(4).count_reducer.js：
				1).reducer的本质是一个函数，接收：preState,action，返回加工后的状态
				2).reducer有两个作用：初始化状态，加工状态
				3).reducer被第一次调用时，是store自动触发的，
								传递的preState是undefined,
								传递的action是:{type:'@@REDUX/INIT_a.2.b.4}

	(5).在index.js中监测store中状态的改变，一旦发生改变重新渲染<App/>
			备注：redux只负责管理状态，至于状态的改变驱动着页面的展示，要靠我们自己写。
~~~



# redux-完整版



![image-20210507221616155](image/image-20210507221616155.png)



![image-20210507221735498](image/image-20210507221735498.png)



![image-20210507221825219](image/image-20210507221825219.png)



![image-20210507223303455](image/image-20210507223303455.png)



![image-20210507223959526](image/image-20210507223959526.png)



![image-20210507224045243](image/image-20210507224045243.png)



![image-20210507224355284](image/image-20210507224355284.png)





**总结**

```
新增文件：
1.count_action.js 专门用于创建action对象
2.constant.js 放置容易写错的type值
```





# redux-异步action求和

![image-20210507232919080](image/image-20210507232919080.png)

actions:

1. 对象action为同步action
2. 函数action为异步action



![image-20210507235416052](image/image-20210507235416052.png)



![image-20210507235505632](image/image-20210507235505632.png)



store只认可action对象，为了让store识别异步action(一个函数),此时需要一个中间件

```shell
yarn add redux-thunk
```

![image-20210507235133665](image/image-20210507235133665.png)



**Counter.jsx**

~~~jsx
import React, { Component } from 'react'
import './index.css'
import store from '../../redux/store.js'
import {createNormalAdd,createNormalSubtract,createAsyncAdd} from '../../redux/counter_actions'
export default class index extends Component {
    state = {carName:'奔驰CS6'}
    componentDidMount(){
    //检测redux中状态的变化，只要变化了，就调用render
    store.subscribe(()=>{
        this.setState({})
    })
    }
    //正常加法
    normalAdd = ()=>{
        let {value} = this.selectRef;
        store.dispatch(createNormalAdd(value,1000))
    }
    //正常减法
    normalSubtract = ()=>{
        let {value} = this.selectRef;
        store.dispatch(createNormalSubtract(value))
    }
    //奇数加
    oddAdd = ()=>{
        let {value} = this.selectRef;
        let counter = store.getState();
        if(counter % 2 !== 0){
            store.dispatch(createNormalAdd(value))
        }
    }
    //异步加
    asyncAdd = ()=>{
    setTimeout(()=>{
        let {value} = this.selectRef;
        store.dispatch(createAsyncAdd(value))
    },1000)
    }
    render() {
        return (
            <div>
                <h1>当前求和为:{store.getState()}</h1>
                <select className="select" ref={(e)=>{this.selectRef = e}}>
                    <option value="1" className="option">1</option>
                    <option value="2" className="option">2</option>
                    <option value="3" className="option">3</option>
                </select>&nbsp;&nbsp;
                <button onClick={this.normalAdd}>+</button>&nbsp;&nbsp;
                <button onClick={this.normalSubtract}>-</button>&nbsp;&nbsp;
                <button onClick={this.oddAdd}>求和奇数加</button>&nbsp;&nbsp;
                <button onClick={this.asyncAdd}>异步加</button>&nbsp;&nbsp;
            </div>
        )
    }
}

~~~

**counter_actions.js**

~~~javascript
/**
 * 该文件专门为Counter组件生成action对象
 */
import {NORMALADD,NORMALSUBTRACT} from './counter_store';
export const createNormalAdd = data=>({type:NORMALADD,data});
export const createNormalSubtract = data=>({type:NORMALSUBTRACT,data});
export const createAsyncAdd = (data,time)=>{
    return (dispatch)=>{
        setTimeout(()=>{
            dispatch(createNormalAdd(data));
        },time)
    }
}
~~~

**store.js**

~~~javascript
/**
 * 该文件专门用于暴露一个store对象，整个应用只有一个store对象
 */
//引入createStore,专门用于创建整个redux中最和兴的store对象
import {createStore,applyMiddleware} from 'redux'
import thunk from 'redux-thunk';
//引入为Counter组件服务的reducer
import counterReducer from './counter_reducer'
//暴露store
export default createStore(counterReducer,applyMiddleware(thunk));
~~~



**总结**

```javascript
(1).明确：延迟的动作不想交给组件自身，想交给action
(2).何时需要异步action：想要对状态进行操作，但是具体的数据靠异步任务返回。
(3).具体编码：
	1).yarn add redux-thunk，并配置在store中
	2).创建action的函数不再返回一般对象，而是一个函数，该函数中写异步任务。
	3).异步任务有结果后，分发一个同步的action去真正操作数据。
(4).备注：异步action不是必须要写的，完全可以自己等待异步任务的结果了再去分发同步action。
```



# react-redux

## 对react-redux的理解



![react-redux模型图](image/react-redux模型图.png)





## 连接容器组件与UI组件

![image-20210509144938586](image/image-20210509144938586.png)



1. container文件夹用来存放创建的容器组件

2. 下载安装

   ```shell
   yarn add react-redux
   ```

   



![image-20210509152228368](image/image-20210509152228368.png)



![image-20210509151947594](image/image-20210509151947594.png)



![image-20210509152315023](image/image-20210509152315023.png)



## react-redux的基本使用

![image-20210509160151664](image/image-20210509160151664.png)



**\5_src_react-redux的基本使用\components\Count\index.jsx--UI组件**

~~~jsx
import React, { Component } from 'react'
export default class Count extends Component {
	state = {carName:'奔驰c63'}
	//加法
	increment = ()=>{
		const {value} = this.selectNumber
		this.props.jia(value*1)
	}
	//减法
	decrement = ()=>{
		const {value} = this.selectNumber
		this.props.jian(value*1)
	}
	//奇数再加
	incrementIfOdd = ()=>{
		const {value} = this.selectNumber
		if(this.props.count % 2 !== 0){
			this.props.jia(value*1)
		}
	}
	//异步加
	incrementAsync = ()=>{
		const {value} = this.selectNumber
		this.props.jiaAsync(value*1,500)
	}
	render() {
		//console.log('UI组件接收到的props是',this.props);
		return (
			<div>
				<h1>当前求和为：{this.props.count}</h1>
				<select ref={c => this.selectNumber = c}>
					<option value="1">1</option>
					<option value="2">2</option>
					<option value="3">3</option>
				</select>&nbsp;
				<button onClick={this.increment}>+</button>&nbsp;
				<button onClick={this.decrement}>-</button>&nbsp;
				<button onClick={this.incrementIfOdd}>当前求和为奇数再加</button>&nbsp;
				<button onClick={this.incrementAsync}>异步加</button>&nbsp;
			</div>
		)
	}
}
~~~





**\5_src_react-redux的基本使用\containers\Count\index.jsx--容器组件**

~~~jsx
//引入Count的UI组件
import CountUI from '../../components/Count'
//引入action
import {
	createIncrementAction,
	createDecrementAction,
	createIncrementAsyncAction
} from '../../redux/count_action'
//引入connect用于连接UI组件与redux
import {connect} from 'react-redux'
/* 
	1.mapStateToProps函数返回的是一个对象；
	2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
	3.mapStateToProps用于传递状态
*/
function mapStateToProps(state){
	return {count:state}
}
/* 
	1.mapDispatchToProps函数返回的是一个对象；
	2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
	3.mapDispatchToProps用于传递操作状态的方法
*/
function mapDispatchToProps(dispatch){
	return {
		jia:number => dispatch(createIncrementAction(number)),
		jian:number => dispatch(createDecrementAction(number)),
		jiaAsync:(number,time) => dispatch(createIncrementAsyncAction(number,time)),
	}
}
//使用connect()()创建并暴露一个Count的容器组件
export default connect(mapStateToProps,mapDispatchToProps)(CountUI)
~~~



**\5_src_react-redux的基本使用\App.jsx--容器组件父组件**

~~~jsx
import React, { Component } from 'react'
import Count from './containers/Count'
import store from './redux/store'
export default class App extends Component {
	render() {
		return (
			<div>
				{/* 给容器组件传递store */}
				<Count store={store} />
			</div>
		)
	}
}
~~~



**\5_src_react-redux的基本使用\index.js--根组件**

~~~jsx
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import store from './redux/store'
ReactDOM.render(<App/>,document.getElementById('root'))
//监测redux中状态的改变，如redux的状态发生了改变，那么重新渲染App组件
store.subscribe(()=>{
	ReactDOM.render(<App/>,document.getElementById('root'))
})
~~~



**总结：**

```
(1).明确两个概念：
	1).UI组件:不能使用任何redux的api，只负责页面的呈现、交互等。
	2).容器组件：负责和redux通信，将结果交给UI组件。
(2).如何创建一个容器组件————靠react-redux 的 connect函数
	connect(mapStateToProps,mapDispatchToProps)(UI组件)
	-mapStateToProps:映射状态，返回值是一个对象
	-mapDispatchToProps:映射操作状态的方法，返回值是一个对象
(3).备注1：容器组件中的store是靠props传进去的，而不是在容器组件中直接引入
(4).备注2：mapDispatchToProps，也可以是一个对象
```



## react-redux优化一：mapDispatch简写



编码层面精简

![image-20210511232308828](image/image-20210511232308828.png)



![image-20210511232929179](image/image-20210511232929179.png)



**\06-react-redux优化一：mapDispatch简写\containers\Counter\index.jsx**

~~~jsx
//引入connect用于连接UI组件与redux
import {connect} from 'react-redux'
//引入Count的UI组件
import CounterUI from '../../components/Counter/index.jsx'
////引入action
import {createNormalAdd,createNormalSubtract,createAsyncAdd} from '../../redux/counter_actions'
import {NORMALADD,NORMALSUBTRACT,ASYNCADD} from '../../redux/counter_store.js'
// function mapStateToProps (state){
//     return {
//         count:state
//     }
// }
// function mapDispatchToProps (dispatch){
//     return {
//         [NORMALADD]:(value)=> {dispatch(createNormalAdd(value*1))},
//         [NORMALSUBTRACT]:(value)=>dispatch(createNormalSubtract(value*1)),
//         [ASYNCADD]:(value,time)=>dispatch(createAsyncAdd(value*1,time))
//     }
// }
// export default connect(mapStateToProps,mapDispatchToProps)(CounterUI);
//mapDispatchToProps的简写
export default connect(state=>({count:state}),{
    [NORMALADD]:createNormalAdd,
    [NORMALSUBTRACT]:createNormalSubtract,
    [ASYNCADD]:createAsyncAdd
})(CounterUI);



~~~



## react-redux优化二：Provider组件的使用

采用**react-redux之后，就不必采用**

~~~javascript
store.subscribe(()=>{
  ReactDOM.render(
      <App />,
    document.getElementById('root')
  );});
~~~

去监听store里面数据的改变了。

![image-20210511235159543](image/image-20210511235159543.png)



## react-redux优化三：整合UI组件与容器组件



![image-20210514225253206](image/image-20210514225253206.png)



![image-20210514225337092](image/image-20210514225337092.png)



**\src\containers\Counter\index.jsx**

~~~jsx
import React, { Component } from 'react'
import './index.css'
import {NORMALADD,NORMALSUBTRACT,ASYNCADD} from '../../redux/counter_store.js'
//引入connect用于连接UI组件与redux
import {connect} from 'react-redux'
////引入action
import {createNormalAdd,createNormalSubtract,createAsyncAdd} from '../../redux/counter_actions'
class Index extends Component {
    state = {carName:'奔驰CS6'}
    //正常加法
    normalAdd = ()=>{
        let {value} = this.selectRef;
        console.log('--',value);
       (this.props[NORMALADD])(value);
    }
    //正常减法
    normalSubtract = ()=>{
        let {value} = this.selectRef;
        this.props[NORMALSUBTRACT](value);

    }
    //奇数加
    oddAdd = ()=>{
        let {value} = this.selectRef;
        if(this.props.count%2 !== 0){
            this.props[NORMALADD](value);
        }
    }
    //异步加
    asyncAdd = ()=>{
        let {value} = this.selectRef;
        this.props[ASYNCADD](value,500);
    }
    render() {
        console.log('Counter.jsx-UI组件',this.props);
        return (
            <div>
                <h1>当前求和为:{this.props.count}</h1>
                <select className="select" ref={(e)=>{this.selectRef = e}}>
                    <option value="1" className="option">1</option>
                    <option value="2" className="option">2</option>
                    <option value="3" className="option">3</option>
                </select>&nbsp;&nbsp;
                <button onClick={this.normalAdd}>+</button>&nbsp;&nbsp;
                <button onClick={this.normalSubtract}>-</button>&nbsp;&nbsp;
                <button onClick={this.oddAdd}>求和奇数加</button>&nbsp;&nbsp;
                <button onClick={this.asyncAdd}>异步加</button>&nbsp;&nbsp;
            </div>
        )
    }
}

// function mapStateToProps (state){
//     return {
//         count:state
//     }
// }
// function mapDispatchToProps (dispatch){
//     return {
//         [NORMALADD]:(value)=> {dispatch(createNormalAdd(value*1))},
//         [NORMALSUBTRACT]:(value)=>dispatch(createNormalSubtract(value*1)),
//         [ASYNCADD]:(value,time)=>dispatch(createAsyncAdd(value*1,time))
//     }
// }
// export default connect(mapStateToProps,mapDispatchToProps)(CounterUI);
//mapDispatchToProps的简写
export default connect(state=>({count:state}),{
    [NORMALADD]:createNormalAdd,
    [NORMALSUBTRACT]:createNormalSubtract,
    [ASYNCADD]:createAsyncAdd
})(Index);
~~~



**总结**

~~~javascript
(1).容器组件和UI组件整合一个文件
(2).无需自己给容器组件传递store，给<App/>包裹一个<Provider store={store}>即可。
(3).使用了react-redux后也不用再自己检测redux中状态的改变了，容器组件可以自动完成这个工作。
(4).mapDispatchToProps也可以简单的写成一个对象
(5).一个组件要和redux“打交道”要经过哪几步？
	(1).定义好UI组件---不暴露
	(2).引入connect生成一个容器组件，并暴露，写法如下：
        connect(
            state => ({key:value}), //映射状态
            {key:xxxxxAction} //映射操作状态的方法
        )(UI组件)
	(4).在UI组件中通过this.props.xxxxxxx读取和操作状态
~~~



## 数据共享-Person组件

![image-20210514232449081](image/image-20210514232449081.png)



![image-20210514234107897](image/image-20210514234107897.png)



**\src\containers\Person\index.jsx**

~~~jsx
import React, { Component } from 'react'
export default class index extends Component {
    addPerson = ()=>{
        let name = this.nameNode.value;
        let age = this.ageNode.value;
        console.log(name,age);
    }
    render() {
        return (
            <div>
                <h2>我是Person组件</h2>
                <div>
                    <input type="text" name="name" placeholder='请输入姓名' ref={(node)=>this.nameNode = node}/>
                </div>
                <div>
                    <input type="number" name="age" placeholder="请输入年龄"  ref={(node)=>this.ageNode = node}/>
                </div>
                <div>
                    <button onClick={this.addPerson}>添加用户</button>
                </div>
                <div>
                    <ul>
                        <li>名字一--年龄一</li>
                        <li>名字二--年龄二</li>
                        <li>名字三--年龄三</li>
                    </ul>
                </div>
            </div>
        )
    }
}
~~~



## 数据共享-完成数据共享

![image-20210515222106996](image/image-20210515222106996.png)



![image-20210515222322074](image/image-20210515222322074.png)



![image-20210515222656829](image/image-20210515222656829.png)



![image-20210515222849954](image/image-20210515222849954.png)



![image-20210515230149969](image/image-20210515230149969.png)





![image-20210515230620687](image/image-20210515230620687.png)



![image-20210515230754704](image/image-20210515230754704.png)



![image-20210515231201285](image/image-20210515231201285.png)



![image-20210515231239676](image/image-20210515231239676.png)



![image-20210515231301013](image/image-20210515231301013.png)



![image-20210515231344208](image/image-20210515231344208.png)



![image-20210515231556650](image/image-20210515231556650.png)



![image-20210515231628655](image/image-20210515231628655.png)



**\redux\store.js**

~~~javascript
/**
 * 该文件专门用于暴露一个store对象，整个应用只有一个store对象
 */
//引入createStore,专门用于创建整个redux中最和兴的store对象
import {createStore,applyMiddleware,combineReducers} from 'redux'
//引入redux-thunk，用于持支异步actions
import thunk from 'redux-thunk';
//引入为Counter组件服务的reducer
import counterReducer from './reducer/counter.js'
import personReducer from './reducer/person.js'
//汇总所有的reducer,成为一个allReducer
const allReducer = combineReducers({
    count:counterReducer, //key为当前的reducer所传递的状态名称，值为容器组件对应的reducer
    personList:personReducer
})
//暴露store
export default createStore(allReducer,applyMiddleware(thunk));
~~~



**\redux\reducer\person.js**

~~~javascript
/**
 * 该文件用于创建一个专门为Person组件服务的reducer。其本质是一个函数
 */
import {ADD_PERSON} from '../constant.js'
const initState = [{
    id:'001',
    name:'silly',
    age:25
}]
const personReducer = function(preState=initState,action){
    const {data,type} = action;
    switch (type) {
        case ADD_PERSON:  //添加一个人
            return [data,...preState]
        default:
            return preState;
    }
}
export default personReducer;
~~~



**\redux\actions\person.js**

~~~javascript
/**
 * 该文件专门为Person组件生成action对象
 */
import {ADD_PERSON} from '../constant'
//创建增加一个人的action动作对象
export const createAddPersonAction = (data)=>({type:ADD_PERSON,data})
~~~



![image-20210516233421663](image/image-20210516233421663.png)



**小结:**

~~~javascript
(1).定义一个Pserson组件，和Count组件通过redux共享数据。
(2).为Person组件编写：reducer、action，配置constant常量。
(3).重点：Person的reducer和Count的Reducer要使用combineReducers进行合并，
	合并后的总状态是一个对象！！！
(4).交给store的是总reducer，最后注意在组件中取出状态的时候，记得“取到位”。
~~~





# 高阶函数与纯函数

![image-20210517232016440](image/image-20210517232016440.png)



![image-20210517232133164](image/image-20210517232133164.png)



## 高阶函数

1. 理解: 一类特别的函数

    情况1: 参数是函数

   情况2: 返回是函数

2. 常见的高阶函数: 

   定时器设置函数

   数组的forEach()/map()/filter()/reduce()/find()/bind()

    promise

   react-redux中的connect函数

3. 作用: 能实现更加动态, 更加可扩展的功能



## 纯函数

1. 一类特别的函数: 只要是同样的输入(实参)，必定得到同样的输出(返回)。

2. 必须遵守以下一些约束 

   不得改写参数数据。

   不会产生任何副作用，例如网络请求，输入和输出设备。

   不能调用Date.now()或者Math.random()等不纯的方法 。

3. redux的reducer函数必须是一个纯函数。

![image-20210517233145539](image/image-20210517233145539.png)

**如果reducer不是一个纯函数，则状态改变之后，将不会响应更新到页面上。**







# redux开发者工具

![image-20210517233800207](image/image-20210517233800207.png)



![image-20210517234326603](image/image-20210517234326603.png)



![image-20210517234602596](image/image-20210517234602596.png)





![image-20210517234724405](image/image-20210517234724405.png)



**总结：**

~~~javascript
(1).yarn add redux-devtools-extension
(2).store中进行配置
	import {composeWithDevTools} from 'redux-devtools-extension'
	const store = createStore(allReducer,composeWithDevTools(applyMiddleware(thunk)))
~~~





# redux最终版

![image-20210518233935759](image/image-20210518233935759.png)



![image-20210518234342815](image/image-20210518234342815.png)



**总结**

~~~javascript
(1).所有变量名字要规范，尽量触发对象的简写形式。
(2).reducers文件夹中，编写index.js专门用于汇总并暴露所有的reducer。
~~~





# 项目打包

~~~shell
--yarn add serve -g
--当前目录 serve
~~~



![image-20210518235430093](image/image-20210518235430093.png)



![image-20210518235456733](image/image-20210518235456733.png)



