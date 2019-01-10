最近写JS碰到了很多对数组的处理。总结出来以便回顾。

> 这里会结合一些函数式编程的思想来进行总结。

# 处理全量数组数据

# 高频出现

- map

    map—> 返回每次函数调用的结果组成的数组**[有返回值]**
        
    ```js
    var baseArr = [1, 2, 3, 4, 5, 6]
    var dealedArr = baseArr.map(params => params + 1)
    
    >>> [ 1, 2, 3, 4, 5, 6 ] // baseArr
    >>> [ 2, 3, 4, 5, 6, 7 ] // dealedArr
    ```

- forEach

    forEach—> 和map的区别就是forEach是**没有**返回值的

    ```js
    var baseArr = [1, 2, 3, 4, 5, 6]
    var dealedArr = []
    baseArr.forEach(params => dealedArr.push(params + 1))
    
    >>> [ 1, 2, 3, 4, 5, 6 ] // baseArr
    >>> [ 2, 3, 4, 5, 6, 7 ] // dealedArr
    ```

- filter

    filter—> filter的返回值是**boolean**的判断，为真则返回到数组中

    ```js
    var judgeArr = [true, false, true, true, false, false]
    var isTrueArr = judgeArr.filter((params)=> params)
    
    >>> [ true, false, true, true, false, false ] // judgeArr
    >>> [ true, true, true ] // isTrueArr
    ```

- reduce

    reduce—> 迭代数组的所有项，然后构建一个最终返回值

    ```js
    // reduce 参数的构造 
    // callbackfn: (previousValue: number, currentValue: number, currentIndex: number, array: number[]) => number
    var numArr = [2, 3, 5, 6, 7]
    var sumArr = numArr.reduce((pV, cV, cI, arr) => {
        console.log(pV, cV, cI, arr)
    })
    >>> 2 3 1 [ 2, 3, 5, 6, 7 ]
    >>> undefined 5 2 [ 2, 3, 5, 6, 7 ]
    >>> undefined 6 3 [ 2, 3, 5, 6, 7 ]
    >>> undefined 7 4 [ 2, 3, 5, 6, 7 ]
    
    var numArr = [2, 3, 5, 6, 7]
    var sumArr = numArr.reduce((pV, cV, cI, arr) => pV + cV)
    
    >>> 23 // sumArr
    
    // 设定初始值
    var numArr = [2, 3, 5, 6, 7]
    var sumArr = numArr.reduce((pV, cV, cI, arr) => pV + cV, 10)
    
    >>> 33 // sumArr
    ```

## 低频出现

- some

    some—> 判断迭代数组是否符合条件，只要有一个为真就返回true

    ```js
    var natrualNumArr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    var evenNum = natrualNumArr.some((params) => params % 2)
    
    >>> true // evenNum
    ```

- every

    every—> 相比some，需要全部为真才返回true

    ```js
    var natrualNumArr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    var evenNum = natrualNumArr.every((params) => params % 2)
    
    >>> false // evenNum
    ```

- reverse

    reverse—> 反转数组

    ```js
    var orderArr = [1, 2, 3, 4, 5]
    var invertedArr = orderArr.reverse()
    
    >>> [1, 2, 3, 4, 5] // orderArr 
    >>> [5, 4, 3, 2, 1] // invertedArr
    ```

# 处理指定数组数据

## 高频出现

- concat

    concat—> 将两个数组合并，并返回一个新数组

    ```js
    var orginArr = [1, 2, 3]
    var etcArr = [true, false, false]
    var mergeArr = orginArr.concat(etcArr)
    
    >>> [ 1, 2, 3, true, false, false ] // mergeArr

    // concat巧用--> 作为数组的deepcopy
    var orginArr = [1, 2, 3, 4, (params) => {
        return params + 1  
    }]
    var copyArr = orginArr.concat()
    orginArr[4] = 'haha'
    
    >>> [ 1, 2, 3, 4, 'haha' ] // orginArr
    >>> [ 1, 2, 3, 4, [Function] ] // copyArr
    ```

- slice

    slice—> 切片，类似于python和golang中的切片。

    传参分别为**start?: number**, **end?: number**

    > 可以实现和concat同样的deepcopy效果

    ```js
    var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    var slice = arr.slice(1, 4)
    
    >>> [ 1, 2, 3, 4, 5, 6, 7, 8, 9 ] // arr
    >>> [ 2, 3, 4 ] // slice
    ```

- splice

    splice—> 用好了很强大，会产生依懒性。emm...

    可以实现删除、插入和替换

    传入参数 **start: number**, **deleteCount?: number**, **...items: T[]**

    ```js
    // ? 删除时，传入两个参数
    var delArr = [1, 3, 5, 7, 9, 11]
    var arrRemoved = delArr.splice(0,2)
    
    >>> [5, 7, 9, 11] // delArr
    >>> [1, 3] // arrRemoved
    
    // ? 插入时， 传入三个参数，start开始位置，deleteCount=0不进行删除，...items可变参
    var insertArr = [1, 3, 5, 7, 9, 11]
    var arrRemoved2 = insertArr.splice(2,0,4,6)
    
    >>> [1, 3, 4, 6, 5, 7, 9, 11] // insertArr
    >>> [] // arrRemoved2 deleteCount=0 返回空数组
    
    var replaceArr = [1,3,5,7,9,11]
    var arrRemoved3 = replaceArr.splice(1,1,2,4)
    
    >>> [ 1, 2, 4, 5, 7, 9, 11 ] // replaceArr
    >>> [3] // arrRemoved3 deleteCount=1 返回被删除的元素
    ```

- push和pop

    push—> 接受可变参 **...items**,返回数组push后长度

    ```js
    var nameArr = ['terry', 'jolie', 'aniki']
    var count = nameArr.push('john', 'scoket')
    
    >>> [ 'terry', 'jolie', 'aniki', 'john', 'scoket' ] // nameArr
    >>> 5 // count
    ```

    pop—> 弹出数组末位，返回元素值

    ```js
    var nameArr = ['terry', 'jolie', 'aniki']
    var popParams = nameArr.pop()
    
    >>> ['terry', 'jolie'] // nameArr
    >>> aniki // popParams
    ```

## 低频出现

- indexOf

    indexOf—> 在数组中找到对应元素下标，没有则返回**-1**

    ```js
    var arr = ['找不到', '也找不到', 'catch', '我隐身啦']
    var hasValue = arr.indexOf('catch')
    var noValue = arr.indexOf('try')
    
    >>> 2 // hasValue
    >>> -1 // noValue
    ```

- shift和unshift

    shift—> 删除原数组第一项，并返回删除**元素的值**；如果数组为空则返回undefined

    ```js
    var arr = ['Jack', 'Sean', 'Lily', 'lucy', 'Tom']
    var params = arr.shift()
    
    >>> Jack // params
    >>> ['Sean', 'Lily', 'lucy', 'Tom'] // arr
    ```

    unshift—> 将参数添加到原数组开头，并返回**数组的长度**

    ```js
    var arr = ['Lily', 'lucy', 'Tom']
    var count = arr.unshift('Jack', 'Sean')
    
    >>> 5 // count
    >>> ['Jack', 'Sean', 'Lily', 'lucy', 'Tom'] // arr
    ```

    好了👌，今天就写这么多了。
