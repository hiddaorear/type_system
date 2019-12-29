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


## change log

- 2019/12/29 create document
