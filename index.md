## Welcome to CC's page!

Edit the page [GO](https://github.com/hong-chen-0/CC.GitHub.io/edit/gh-pages/index.md)

### REACT环境搭建流程

条件
Node >= 10.16 和 npm >= 5.6，（配合云端硬盘react-demo文件使用）

安装REACT
npx create-react-app my-app
cd my-app

安装ROUTER
npm install react-router-dom --save-dev

安装REDUX
npm install --save redux

安装axios
npm install -save axios
```markdown
//axios,localStorage的使用

import React from 'react';
import '../../assets/login.css'
import axios from 'axios'

class login extends React.Component {
    constructor(props) {
		super(props);
        this.state={
        }
    }
    goLogin=()=>{
        let idData = "id="+ this.refs.loginID.value+"&"+"pw="+ this.refs.loginPW.value;
        axios.post('/index.php',idData)
            .then((res)=>{
                console.log('axios 获取数据成功:'+JSON.stringify(res.data[0].router))
                if(res.data[0].login == 'yes'){
                    const token = res.data[0].token;
                    localStorage.setItem('Tokenkey',token)
                    localStorage.setItem('TokeyTime',Date())
                    this.props.history.push(res.data[0].router)
                }else{
                    this.props.history.push(res.data[0].router)
                }
            })
            .catch((error)=>{console.log('axios 获取数据失败'+error)})
    }
    render(){
        return (
        <div className='login-main'>
            <div className='login-form flex-center'>
                <div>
                    账号登录
                </div>
                <div className='flex-middle'>
                    账： <input ref='loginID'></input>
                </div>
                <div className='flex-middle'>
                    密： <input ref='loginPW'></input>
                </div>
                <button onClick={this.goLogin}>登录</button>
            </div>
        </div>
        );
    }
}

export default login;
```

```markdown
//redux使用

//reducer/index.js

import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
) // 创建数据存储仓库
export default store   //暴露出去

//reducer/reducer.js

const defaultState = {
    inputValue : 'inputValue',
}
 //eslint-disable-next-line
 export default (state = defaultState,action)=>{
    switch (action.type) {
        case 'changeInput':
            let changeInput = JSON.parse(JSON.stringify(state)) //深度拷贝state
            changeInput.inputValue = action.value
            return changeInput    
        default:
            return state
    }
}

//reducer/actionCreators.js

export const changeInputValue=(value)=>({
    type:'changeInput',
    value
})

//page/listDetail

import React from 'react';
import store from '../../reducer'
import {changeInputValue} from '../../reducer/actionCreators'

class listDetail extends React.Component {
  constructor(props) {
		super(props);
    this.state={
      inputValue:store.getState().inputValue,
    }
    store.subscribe(this.storeChange) //订阅Redux的状态
	}
  storeChange=()=>{
    this.setState(store.getState())
  }
  componentWillUnmount() {
    this.setState = (state, callback) => {
      return
    }
  }//在组件销毁的时候将异步方法撤销
  changeInput=()=>{
    const action = changeInputValue('改变的inputValue')
    store.dispatch(action)
  }
  changeInput2=()=>{
    const action = changeInputValue('再改变的inputValue')
    store.dispatch(action)
  }
  render(){
    return (
      <div style={{'fontSize':'20px'}}>
        pageIndex
        <br/>
        {this.state.inputValue}
        <br/>
        <button onClick={this.changeInput}>改变redux</button>
        <button onClick={this.changeInput2}>再改变redux</button>
      </div>
    );
  }
}

export default listDetail;
```
