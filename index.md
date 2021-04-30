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

### router的使用

```markdown

//index.js----------------------------------------------------------------------------

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

ReactDOM.render(
    <App />,
  document.getElementById('root')
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();

//App.js----------------------------------------------------------------------------

import React from 'react';
import './assets/App.css';
import { HashRouter as Router,Route,Switch,Redirect} from 'react-router-dom';
import pageIndex from './views/page/pageIndex';
import homeIndex from './views/home/homeIndex';
import login from './views/login/login';

class App extends React.Component  {
  constructor(props) {
    super(props);
    this.state={
    }
  }
  render(){
    return (
      <div className="App">
          <Router>
            <Switch>
              <Route path="/pageIndex" component={pageIndex} />
              <Route path="/homeIndex" component={homeIndex} />
              <Route path="/login" component={login} />
              <Redirect from="/*" to="/login" />
            </Switch>
          </Router>
      </div>
    );
  }
}

export default App;


```

### axios,localStorage,token的使用

```markdown

//login/login.js----------------------------------------------------------------------------

import React from 'react';
import '../../assets/login.css'
import axios from 'axios'
import store from '../../reducer';
import {changeToken} from '../../reducer/actionCreators'

class login extends React.Component {
    constructor(props) {
		super(props);
        this.state={
            localToken:store.getState().localToken,
        }
        store.subscribe(this.storeChange) //订阅Redux的状态
    }
    storeChange=()=>{
        this.setState(store.getState())
    }
    //在组件销毁的时候将异步方法撤销
    componentWillUnmount() {
        this.setState = (state, callback) => {
            return
        }
    }
    goLogin=()=>{
        let arr ={
            'id' : this.refs.loginID.value,
            'pw' : this.refs.loginPW.value,
        }
        let idData = "contentAll="+JSON.stringify(arr);
        //let idData = "id="+ this.refs.loginID.value+"&"+"pw="+ this.refs.loginPW.value;
        axios.post('/index.php',idData)
            .then((res)=>{
                console.log('axios 获取数据成功:'+JSON.stringify(res.data[0].router))
                if(res.data[0].login === 'yes'){
                    //存储TOKEN到本地localStorage
                    const token = res.data[0].token;
                    localStorage.setItem('Tokenkey',token)
                    localStorage.setItem('TokeyTime',Date())
                    //存储TOKEN到STORE
                    const action = changeToken({'Tokenkey':token,'TokeyTime':Date()})
                    store.dispatch(action)
                    //路由跳转
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
                    HAYA商家端登录{this.state.localToken.Tokenkey}{this.state.localToken.TokeyTime}
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

//index.php----------------------------------------------------------------------------

<?php

$data = json_decode($_POST["contentAll"]);

$id = $data->id;
$pw = $data->pw;

$res;
$login;

if($id=='HC' && $pw=='HC'){
    $res = '/homeIndex';
    $login = 'yes';
}else{
    $res = '/login';
    $login = 'no';
}

$list = array(
    array("login" => $login,"router" => $res, "token"=>'dgdg436gsdfg35vbddsd345')
);

echo json_encode($list);

```

### redux的使用

```markdown
//reducer/index.js----------------------------------------------------------------------------

import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
) // 创建数据存储仓库
export default store   //暴露出去

//reducer/reducer.js----------------------------------------------------------------------------

const defaultState = {
    inputValue : 'inputValue',
    localToken:{
        Tokenkey:'',
        TokeyTime:''
    }
}
 //eslint-disable-next-line
 export default (state = defaultState,action)=>{
    switch (action.type) {
        case 'changeInput':
            let changeInput = JSON.parse(JSON.stringify(state)) //深度拷贝state
            changeInput.inputValue = action.value
            return changeInput    
        case 'changeToken':
            let changeToken = JSON.parse(JSON.stringify(state)) //深度拷贝state
            changeToken.localToken = action.value
            return changeToken       
        default:
            return state
    }
}

//reducer/actionCreators.js----------------------------------------------------------------------------

export const changeInputValue=(value)=>({
    type:'changeInput',
    value
})
export const changeToken=(value)=>({
    type:'changeToken',
    value
})

//page/listDetail.js（展示及改变STORE）--------------------------------------------------------

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

//page/pageIndex.js(在路由分支注入TOKEN路由守卫)--------------------------------------------------

import React from 'react';
import { Route,Switch,Redirect} from 'react-router-dom';
import listDetail from './listDetail';
import underWayList from './underWayList';
import store from '../../reducer';

class pageIndex extends React.Component {
  constructor(props) {
		super(props);
    this.state={
        localToken:store.getState().localToken,
    }
    //订阅Redux的状态
    store.subscribe(this.storeChange)
  }
  storeChange=()=>{
    this.setState(store.getState())
  }
  //在组件销毁的时候将异步方法撤销
  componentWillUnmount() {
    this.setState = (state, callback) => {
        return
    }
  }
  //打开页面前检查STORE里的TOKEN和本地localStorage里的TOKEN是否一致
  componentDidMount(){
    if(this.state.localToken.Tokenkey==localStorage.getItem("Tokenkey")){
      return
    }else{
      this.props.history.push('/login')
    }
  }
  render(){
      return (
      <div>
        <Switch>
          <Route path="/pageIndex/listDetail" component={listDetail} />
          <Route path="/pageIndex/underWayList" component={underWayList} />
          <Redirect from="/pageIndex" to="/pageIndex/listDetail" />
        </Switch>
      </div>
      );
  }
}

export default pageIndex;

```
