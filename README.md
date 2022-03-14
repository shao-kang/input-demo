
# input组件
说明 当@前为字母和数字时按照邮箱处理， 当@前为汉子 空格时调起用户选择窗口
# 组件属性
1. users
可取值为
```
{
    name: "1212", // 用户名称
    id: "dsdfds", // 唯一id
    img: "dfds",
  },
```
# 组件事件
1. changeUserName
 用户名更改 可配合 users 属性做远程请求 e 为string
2. change
    输入文本以及 user 列表更改事件
    ```
    change(e) {
        e.textValue // 文案内容
        e.users // 用户列表
    }
    ```