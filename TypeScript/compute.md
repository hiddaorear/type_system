# 类型的运算

## 集合运算

### 交集

### 并集

## 加减

## 模拟模式匹配

**discriminated union**

``` TypeScript
type Square = {
    kind: 'square'
    size: number
}

type Rectangle = {
    kind: 'rectangle'
    size: number
}

type Shape = Square | Rectangle

function area(shape: Shape): number {
    switch (shape.kind) {
        case 'square':
            return shape.size * shape.size
        case 'rectangle':
            return shape.size * shape.size
        default:
            return 0
    }
}


```


## 构造类型

### 用字面量构造类型

字面量有初始值，用`typeof`，减少重复代码。

``` TypeScript
const defaultOption = {
    timeout: 500
}

type Opt = typeof defaultOption

```


## change log

- 2019/12/29 create document
