# React笔记

> 特点：
>
> 1. 声明式编码（Declarative）
> 2. 组件化编码（Component-Based）
> 3. 支持客户端与服务器渲染（Learn Once，Write Anywhere）
> 4. 高效
>    + 虚拟（virtual）DOM，不总是直接操作DOM
>    + DOM Diff 算法，最小化页面重绘
> 5. 单向数据流

## 一、JSX简介

~~~javascript
const element = <h1>Hello, world!</h1>;
~~~

> JSX是一种标签语法，它既不是字符串也不是HTML。JSX也是一个表达式，在编译之后，JSX 表达式会被转为普通 JavaScript 函数调用，并且对其取值后得到 JavaScript 对象。

1. **JSX特定属性**：

   > 你可以通过使用引号，来将属性值指定为字符串字面量:
   >
   > ~~~javascript
   > const element = <div tabIndex="0"></div>;
   > const element2 = <img src="images/01.jpg" />;		//可以直接使用 /> 闭合标签
   > ~~~
   >
   > 也可以使用大括号，来在属性值中插入一个 JavaScript 表达式:
   >
   > ~~~javascript
   > const element = <img src={user.avatarUrl}></img>;	//直接使用大括号可以防止注入攻击
   > ~~~

2. **JSX 表示对象**

   > Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用

   ~~~javascript
   const element1 = (
     <h1 className="greeting">
       Hello, world!
     </h1>
   );
   const element2 = React.createElement(
     'h1',
     {className: 'greeting'},
     'Hello, world!'
   );
   //element1 和 element2 代码完全等效
   ~~~



## 二、元素渲染

语法：

~~~javascript
ReactDOM.render(element, container[, callback])
~~~

> 1. 会控制你传入容器节点里的内容。当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM 差分算法（DOM diffing algorithm）进行高效的更新。
> 2. 不会修改容器节点（只会修改容器的子节点）。可以在不覆盖现有子节点的情况下，将组件插入已有的 DOM 节点中。

例子：`写一个hello world`

~~~javascript
//html
<div id="root"></div>
//js
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
~~~



## 三、面向组件编程

### 1. 自定义组件

`html`:

~~~html
<div id="example1"></div>
<div id="example2"></div>
~~~

`js`:

~~~javascript
//方式1：工厂函数组件（简单组件）
function MyComponent(){
    return <h2>工厂函数组件（简单组件）</h2>
}
ReactDOM.render(<MyComponent />,document.getElementById('example1'))
//方式2：ES6类组件（复杂组件）
class MyComponent2 extends React.Component{
    render(){
        return <h2>ES6类组件（复杂组件）</h2>
    }
}
ReactDOM.render(<MyComponent2 />,document.getElementById('example2'))
~~~



### 2. 组件的三大属性

#### 1> `State`

> `state（状态）` 是组件对象的重要属性，值是对象（可以包含多个数据）
>
> 组件被称为“状态机”，通过更新组件的 `state` 来更新对应页面的显示（重新渲染组件）

例子：

~~~js
//1. 创建组件类
class Like extends React.Component {
    //构造函数中初始化state
    constructor(props) {
        super(props);
        this.state = {
            isLikeEat: false
        }
        //将新增的方法，强制绑定为组件对象
        this.handleClick = this.handleClick.bind(this)
    }

    render() {
        //读取状态
        const {isLikeEat} = this.state
        return <h2 onClick={this.handleClick}>{isLikeEat ? '我喜欢吃' : '我讨厌吃'}</h2>
    }

    handleClick() {
        const isLikeEat = !this.state.isLikeEat
        //更新状态
        this.setState({isLikeEat})
    }
}

//2. 渲染组件
ReactDOM.render(<Like/>, document.getElementById('like_'))
~~~



#### 2> `Props`

> `Props（属性）`

例子：

~~~javascript
    //1. 创建组件
    function Person(props) {
        return (
            <ul>
                <li>名称：{props.name}</li>
                <li>年龄：{props.age}</li>
                <li>性别：{props.gender}</li>
            </ul>
        )
    }

    //指定属性默认值
    Person.defaultProps = {
        age: 18,
        gender: '女'
    }

    //对属性进行类型限制和必要性限制
    Person.propTypes = {
        name: PropTypes.string.isRequired,
        age: PropTypes.number.isRequired
    }

    const p1 = {name: '没救', age: 12, gender: '男'}

    //2. 渲染组件
    ReactDOM.render(<Person {...p1}/>, document.getElementById('person_1'))
    ReactDOM.render(<Person name="花花"/>, document.getElementById('person_2'))
~~~



#### 3> `Ref`

例子：

~~~javascript
    //1. 创建组件类
    class MyComponent extends React.Component {
        constructor(props) {
            super(props);
            this.blueInput = this.blueInput.bind(this)
            this.showInput = this.showInput.bind(this)
        }

        render() {
            return (
                <div>
                    <input type="text" ref="content"/>&nbsp;&nbsp;
                    <button onClick={this.showInput}>点击提示输入内容</button>
                    <input type="text" ref={input => this.input_ = input}
                           onBlur={this.blueInput} placeholder="失去焦点提示内容"/>
                </div>
            );
        }

        showInput() {
            //官方不推荐此写法
            const input = this.refs.content
            alert(input.value)
        }

        blueInput() {
            alert(this.input_.value)
        }
    }

    //2. 渲染组件
    ReactDOM.render(<MyComponent/>, document.getElementById('box'))
~~~



#### 4> 整合复杂控件

~~~javascript
    //1. 创建组件类
    //父组件，最上层的组件
    class App extends React.Component {
        constructor() {
            super();
            this.state = {
                todoList: ['吃饭', '睡觉', '玩游戏']
            }
            this.addTodo = this.addTodo.bind(this)
        }

        render() {
            const {todoList} = this.state
            return (
                <div>
                    <h1>Simple TODO List</h1>
                    <AddComponent next={todoList.length + 1} addTodo={this.addTodo}/>
                    <TodoList list={todoList}/>
                </div>
            );
        }

        addTodo(todo) {
            const {todoList} = this.state
            todoList.unshift(todo)
            this.setState({todoList})
        }
    }

    //增加控件，用来增加todo list
    class AddComponent extends React.Component {
        constructor() {
            super();
            this.handlerAddClick = this.handlerAddClick.bind(this)
            this.handlerKeyDown = this.handlerKeyDown.bind(this)
        }

        render() {
            return (
                <div>
                    <input type="text" onKeyDown={this.handlerKeyDown} 
        				ref={input => this.input = input}/>
                    <button onClick={this.handlerAddClick}>Add #{this.props.next}</button>
                </div>
            );
        }

        handlerAddClick() {
            let text = this.input.value.trim()
            if (text) {
                this.props.addTodo(text)
                this.input.value = ''
            }
        }

        handlerKeyDown(e) {
            //回车进入
            if (e.keyCode === 13) {
                this.handlerAddClick()
            }
        }
    }
    AddComponent.propTypes = {
        next: PropTypes.number.isRequired,
        addTodo: PropTypes.func.isRequired
    }

    //todo list 控件，显示待办内容
    class TodoList extends React.Component {
        render() {
            return (
                <div>
                    <ul>
                        {
                        this.props.list.map((item, index) => <li key={index}>{item}</li>)
                        }
                    </ul>
                </div>
            );
        }
    }
    TodoList.propTypes = {
        list: PropTypes.array.isRequired
    }

    //2. 渲染组件
    ReactDOM.render(<App/>, document.getElementById('box'))
~~~



### 3. 收集表单数据

+ **受控组件**

  > HTML中使用state属性维护表单元素状态，并且只能通过`setState()`来更新。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

+ **非受控组件**

  > React推荐使用`受控组件`来处理表单数据，在一个受控组件中，表单数据是由 React 组件来管理的。另一种替代方案是使用`非受控组件`，这时表单数据将交由 DOM 节点来处理。 

  ~~~javascript
  class NameForm extends React.Component {
    constructor(props) {
      super(props);
      this.handleSubmit = this.handleSubmit.bind(this);
      this.input = React.createRef();
    }
  
    handleSubmit(event) {
      alert('A name was submitted: ' + this.input.current.value);
      event.preventDefault();
    }
  
    render() {
      return (
        <form onSubmit={this.handleSubmit}>
          <label>
            Name:
            <input type="text" ref={this.input} />
          </label>
          <input type="submit" value="Submit" />
        </form>
      );
    }
  }
  ~~~




### 4. 生命周期

> 声明式编程：类似jquery，不需要关注功能具体实现的过程，调用就行

[参考资料](https://juejin.im/post/6844903510538977287)  [参考资料2](https://juejin.im/post/6844904005152276487)  [参考资料3](https://segmentfault.com/a/1190000020348448)

![](./images/react-smzq.jpg)

1. **初始化阶段**

+ 设置组件的默认属性

```
static defaultProps = {
    name: 'sls',
    age:23
};
//or
Counter.defaltProps={name:'sls'}
```

+ 设置组件的初始化状态

```
constructor() {
    super();
    this.state = {number: 0}
}
```

+ `componentWillMount()`：组件即将被渲染到页面之前触发，此时可以进行开启定时器、向服务器发送请求等操作

+ `render()`：组件渲染

+ `componentDidMount()`：组件已经被渲染到页面中后触发：此时页面中有了真正的DOM的元素，可以进行DOM相关的操作

2. **运行中阶段**

+ `componentWillReceiveProps()`：组件接收到属性时触发

+ `shouldComponentUpdate()`：当组件接收到新属性，或者组件的状态发生改变时触发。组件首次渲染时并不会触发

```
//该钩子函数可以接收到两个参数，新的属性和状态，返回true/false来控制组件是否需要更新。
shouldComponentUpdate(newProps, newState) {
    if (newProps.number < 5) return true;
    return false
}
```

+ `componentWillUpdate()`：组件即将被更新时触发

+ `componentDidUpdate()`：组件被更新完成后触发。页面中产生了新的DOM的元素，可以进行DOM操作

3. **销毁阶段**

+ `componentWillUnmount()`：组件被销毁时触发。这里我们可以进行一些清理操作，例如清理定时器，取消Redux的订阅事件等等。



### 5. 虚拟DOM 和DOM Diff算法