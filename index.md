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
