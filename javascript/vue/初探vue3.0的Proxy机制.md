> 19年已经来了，尤大也在11月份宣布了改版并发布vue3.0的计划，大概的时间会在19年的年中或者年底完成稳定版的发布。在11月份的关于vue的技术讨论会上，以尤大领头的vue团队，透露了有关vue3.0比较大的变动。其中有一项是关于Proxy机制的。本文就是初探Vue3.0中的Proxy。

# 一、初探Proxy？

说到Proxy大家可能不熟悉，但是提起vue2.x版本中的Object.defineProperty,大家应该是有所了解的。

前端面试中vue常考的内容之一，必然是Object.defineProperty。

## 说说为什么要取代Object.defineProperty?

1. 经常使用vue中watch的小伙伴应该知道一种情况，就是在watch数组的时候，数组内元素的变化是不会体现和在watch中实现的，只有在watch中强制添加deep属性才能进行监听和使用。

    在观看了vue的源码后，发现vue经过一系列的内部处理，可以使用以下几种方法来监听数组。（**[Vue为什么不能检测数组变动](https://segmentfault.com/a/1190000015783546)）**

        push()
        pop()
        shift()
        unshift()
        splice()
        sort()
        reverse()

    由于只针对了八种方法进行hack处理，其他数组方法扩展时就显得力不从心了。

2. Object.defineProperty只能劫持对象的属性，因此我们需要对每个属性进行遍历。

    Vue里，是通过递归以及遍历data 对象来实现对数据的监控的，如果属性值也是对象那么需要深度遍历,显然如果能劫持一个完整的对象，不管是对操作性还是性能都会有一个很大的提升。

相比而言，Proxy具有的优点：

- 可以劫持整个对象，并返回一个新对象。
- 有13中劫持操作。

估计有小伙伴就要问了，ES6的规范不是已经出了很久了么，为什么Proxy没有在vue2.x的版本中实现呢。

> Proxy是es6提供的新特性，兼容性不好，最主要的是这个属性无法用polyfill来兼容

**目前Proxy并没有有效的兼容方案，未来大概会是3.0和2.0并行，需要支持IE的选择2.0**

# 二、深入浅出Proxy?

1. 含义
- Proxy是 ES6 中新增的一个特性，翻译过来意思是"代理"，用在这里表示由它来“代理”某些操作。
- Proxy 让我们能够以简洁易懂的方式控制外部对对象的访问。其功能非常类似于设计模式中的代理模式。
- Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。
- 使用 Proxy 的核心优点是可以交由它来处理一些非核心逻辑（如：读取或设置对象的某些属性前记录日志；设置对象的某些属性值前，需要验证；某些属性的访问控制等）。

从而可以让对象只需关注于核心逻辑，达到关注点分离，降低对象复杂度等目的。

2. 基本用法

    let p = new Proxy(target, handler);

- **target**是用Proxy包装的被代理对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。
- **handler**是一个对象，其声明了代理target 的一些操作，其属性是当执行一个操作时定义代理的行为的函数。
- **p**是代理后的对象。当外界每次对 p 进行操作时，就会执行 handler 对象上的一些方法。

Proxy共有13种劫持操作，handler代理的一些常用的方法有如下几个：

    get:       读取
    set:       修改
    has:       判断对象是否有该属性
    construct: 构造函数

3. 示例:

下面就用Proxy来定义一个对象的get和set，作为一个基础demo

    let obj = {};
     let handler = {
       get(target, property) {
        console.log(`${property} 被读取`);
        return property in target ? target[property] : 3;
       },
       set(target, property, value) {
        console.log(`${property} 被设置为 ${value}`);
        target[property] = value;
       }
     }
    
     let p = new Proxy(obj, handler);
     p.name = 'tom' //name 被设置为 tom
     p.age; //age 被读取 3

*p 读取属性的值时，实际上执行的是 handler.get() ：在控制台输出信息，并且读取被代理对象 obj 的属性。*

*p 设置属性值时，实际上执行的是 handler.set() ：在控制台输出信息，并且设置被代理对象 obj 的属性的值。*

# 三、基于Proxy来实现双向绑定

用Proxy来实现一个经典的双向绑定todolist

    <div id="app">
          <input type="text" id="input" />
          <div>您输入的是： <span id="title"></span></div>
          <button type="button" name="button" id="btn">添加到todolist</button>
          <ul id="list"></ul>
     </div>

先来一个Proxy，实现输入框的双向绑定显示：

    const obj = {};
        const input = document.getElementById("input");
        const title = document.getElementById("title");
        const list = document.getElementById("list");
        const btn = document.getElementById("btn");
    
        const newObj = new Proxy(obj, {
          get: function(target, key, receiver) {
            console.log(`getting ${key}!`);
            return Reflect.get(target, key, receiver);
          },
          set: function(target, key, value, receiver) {
            console.log(target, key, value, receiver);
            if (key === "text") {
              input.value = value;
              title.innerHTML = value;
            }
            return Reflect.set(target, key, value, receiver);
          }
        });
    
        input.addEventListener("keyup", function(e) {
          newObj.text = e.target.value;
        });

Tips: [涉及到Reflect属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

接下来就是添加todolist列表，先把数组渲染到页面上去：

    // 渲染todolist列表
        const Render = {
          // 初始化
          init: function(arr) {
            const fragment = document.createDocumentFragment();
            for (let i = 0; i < arr.length; i++) {
              const li = document.createElement("li");
              li.textContent = arr[i];
              fragment.appendChild(li);
            }
            list.appendChild(fragment);
          },
          addList: function(val) {
            const li = document.createElement("li");
            li.textContent = val;
            list.appendChild(li);
          }
        };

再来一个Proxy，实现Todolist的添加：

    const arr = [];
        // 监听数组
        const newArr = new Proxy(arr, {
          get: function(target, key, receiver) {
            return Reflect.get(target, key, receiver);
          },
          set: function(target, key, value, receiver) {
            console.log(target, key, value, receiver);
            if (key !== "length") {
              Render.addList(value);
            }
            return Reflect.set(target, key, value, receiver);
          }
        });
    
        // 初始化
        window.onload = function() {
          Render.init(arr);
        };
    
        btn.addEventListener("click", function() {
          newArr.push(parseInt(newObj.text));
        });

[代码可参考](https://github.com/nightzing/vue3-Proxy/blob/master/proxy.html)

# 四、基于Proxy来实现vue的观察者机制

1. Proxy实现observe

        observe(data) {
                const that = this;
                let handler = {
                 get(target, property) {
                    return target[property];
                  },
                  set(target, key, value) {
                    let res = Reflect.set(target, key, value);
                    that.subscribe[key].map(item => {
                      item.update();
                    });
                    return res;
                  }
                }
                this.$data = new Proxy(data, handler);
              }

这段代码里把代理器返回的对象代理到this.$data，即this.$data是代理后的对象，外部每次对this.$data进行操作时，实际上执行的是这段代码里handler对象上的方法。

2. compile和watcher

比较熟悉vue的同学都很清楚，vue2.x在 new Vue() 之后。 Vue 会调用 _init 函数进行初始化，它会初始化生命周期、事件、 props、 methods、 data、 computed 与 watch 等。

其中最重要的是通过 Object.defineProperty 设置 setter 与 getter 函数，用来实现「响应式」以及「依赖收集」。

而我们上面已经用Proxy取代了Object.defineProperty这部分观察者机制，而要实现整个基本mvvm双向绑定流程。

除了observe还需要compile和watche等一系列机制，我们这里像模板编译的工作就不展开描述了，为了实现基于Proxy的vue添加Totolist,这里只写了 compile和watcher来支持observe的工作。
