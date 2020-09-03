# drag_size

## 插件安装

```
npm i drag_size
```

### 插件的使用

```
在入口文件main.js中使用

import dragSize from "drag_size";
import 'drag_size/lib/drag_size.css'

Vue.use(dragSize);
```

### 说明文档 -- 属性

| 属性         | 说明                                           | 默认值                            |
| ------------ | ---------------------------------------------- | --------------------------------- |
| value        | 绑定的值,                                      | Object \|{x:0,y:0,w:0,h:0,url:''} |
| disabled     | 是否禁用拖动功能                               | Boolean \| false                  |
| deleteImg    | 是否可以删除当前图片，需绑定 on-delete-img事件 | Boolean \| false                  |
| limitRangeId | 限制的拖动范围 默认现在在body                  | String  \| ''                     |
| id           | 当前img标签的id                                | String Number \| 3                |
| scrollTop    | 插件所在dom距离顶部的滚动距离                  | Number  \| 0                      |
| scrollLeft   | 插件所在dom距离左边的滚动距离                  | Number  \| 0                      |

<!--因为需求不明  所以滚动距离需要使用者手动传递 -->

```
mounted() {  //获取指定dom滚动距离的例子  具体根据实际情况进行开发
    let scrollToBody = document.getElementById('id')
    scrollToBody.onscroll = () => {
      this.$nextTick(() => {
        this.scrollTop = scrollToBody.scrollTop
      })
    }
  },
```

![image-20200903130804282](https://i.loli.net/2020/09/03/UQxy43wFGDameLo.png)

### 说明文档 -- 事件

| 事件          | 说明                   | 返回值 |
| ------------- | ---------------------- | ------ |
| on-delete-img | 点击删除按钮触发的事件 | 无     |

[查看详情](https://github.com/Zhoushunshun541/drag_size).

