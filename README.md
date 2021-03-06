### state

state(状态)：负责维护组件内部的状态。通过`steState`方法修改`state`（直接修改`this.state`无效），继而重新渲染用户界面。

### 初始化state

```
//ES5
var MyComponent=React.createClass({
    //使用getInitialState 方法创建
  getInitialState: function() {
    return {
      loopsRemaining: this.props.maxLoops,
    };
  }
}

//ES6  
class MyComponent extends React.Component{
    constructor(){
        super()
        //在constructor中的super()后创建
        this.state={
    
        }
    }
}
//ES6 
class MyComponent extends React.Component{
    //直接创建一个state对象
    state={
        
    }
}
```

### staState
#### 用法一
`setState(states,callback)` 接收两个参数。第一个以一个对象的形式传入用于合并组件内部的`state`（如果之前有定义那么覆盖，没有定义就新建）,第二个参数为当前的state改变完成后的回调（一般很少用到）。

`setState` `不是同步更新`，而是`异步更新`的。`React`会将多个`steState`缓存起来，然后一次性更新。

举个例子：
```
    changeName = () =>{
        this.steState({
            firstName:"你好，"
        })
        this.steState({
            lastName:"世界"
        })
    }
```
在这段代码中调用了`2`次`this.steState()`,但是只会触发一次更新。

正因为不会同步更新`state`，所以用这个传入对象的方式修改`state`的时候要注意`不应该依靠它们的值来计算下一个状态`。

举个例子：
```
state={
    age:18
}
changeAge= ()=>{
    this.setState({
            age:this.state.age+1
        })
    this.setState({
            age:this.state.age+1
        })
    this.setState({
            age:this.state.age+1
        })
}

```
在这段代码中，连续调用了`3`次`this.setState()`。然而运行的结果却不能达到我们的预期，我们希望运行的结果是`age`连续`3`次增加`1`。可是最后的结果却是只增加`1`。所以我们再使用传入对象的方式时，不能依赖前一次`setState`的结果来进行下一次`steState`。如果需要该种操作。建议使用方法二。

        ps1:如果使用该种形式 如:
        this.setState({
                age:this.state.age+1
            })
        在键值对的值中，不能使用`++ --` 而要使用`+1 -1` 的形式赋值。否则赋值失败。

        如果使用下面的用法二 则可以使用`++ --`的形式赋值



#### 用法二
`setState((state,props)=>{})` 接收一个函数为参数。该函数将前一次`setState`的结果作为第一个参数，并且前一次操作的`props`的值作为第二个参数;

所以上面的例子可以改写为
```
state={
    age:18
}
changeAge=()=>{
    function add(state){
        age:state.age++
    }
    this.setState(add)
    this.setState(add)
    this.setState(add)
}
```
修改后的例子就可以达到预期的效果了

    ps:如果用法一和用法二混用呢？
    state={
        age:18
    }
    changeAge=()=>{
        function add(state){
            age:state.age++
        }
        this.setState(add)
        this.setState(add)
        this.setState(add)
        this.steState({
            age:this.state.age+1
            })
        this.setState(add)
        this.setState(add)    
    }

    如果有兴趣可以做一下测试，我这里直接给出结果。结果是增加了`3`，不是增加了`6`
    因为`setState`不论传入对象还是函数都不会同步更新。所以在上面的例子中，虽然我们先调用了三次传出函数的`setState`，但是在中间调用了一个传对象的`setState`，所以这里可以认为是前面三次更新的结果会失效，从传对象的`setState`开始重新缓存。


setState一般和事件一起使用，举一个例子

```
// 这里的HelloWrold（组件名） 首字母一定要大写

class HelloWrold extends React.Component{
            constructor(){
                super()
                //初始化state
                this.state={
                    firstName:"hello ",
                    lastName:"world",
                    click:0
                }
            }
            render(){
                return (
                    <div onClick={this.showClick}>
                        <span onClick={this.changeFirstName}>{this.state.firstName}</span>
                        <span onClick={this.changeLastName}>{this.state.lastName}</span>
                        <p>您当前点击了{this.state.click}次</p>
                    </div>
                )
            }

            /*这里注意 如果使用es6的语法，一定要用箭头函数，或者改变this指向。如果直接定义
            shouClick(){
                //dosomething
            }
            因为this的指向问题 会报错*/

            showClick=(e)=>{
                this.setState({click:this.state.click+1})
            }
            changeLastName=(e)=>{
                this.setState({
                    lastName:"世界"
                })
            }
            changeFirstName=(e)=>{
                this.setState({
                    firstName:"你好,"
                })
            }
        }

        ReactDOM.render(
            <HelloWrold/>,
            document.getElementById("demo")
            )

        //页面效果 hello world 您当前点击了0次
        //点击hello后变成 你好,world 您当前点击了1次
        //再次点击world后变成 你好，世界 您当前点击了2次
        ```

        ```
        //ES5 重写

        var HelloWrold = React.createClass({
            getInitialState:function(){
                return{
                    firstName:"hello ",
                    lastName:"world",
                    click:0
                }
            },
            /*使用ES5的语法是 React已经将this自动绑定在当前类上 所以这里可以定义而不需要像ES6语法中的那样需要重新绑定*/
            showClick:function(){
                this.setState({click:this.state.click+1})
            },
            //这里省略一些代码......
            render:function(){
                return(
                    <div onClick={this.showClick}>
                        <span onClick={this.changeFirstName}>{this.state.firstName}</span>
                        <span onClick={this.changeLastName}>{this.state.lastName}</span>
                        <p>您当前点击了{this.state.click}次</p>
                    </div>  
                )
            }
        })
        
        ```
