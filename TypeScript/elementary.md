# 基础

## 和类型

`Dinner`要么有`fish`，要么有`bear`，不存在两者都有的情况。

``` TypeScript

type Dinner = { fish: number } | { bear: string }
const d: Dinner = { fish: 1, bear: '1' }

if ('fish' in d) {
    // `d` has `fish` and no `bear` here.
} else {
    // `d` has `bear` and no `fish` here.
}


```

## 积类型

## 查找类型

### 复用复杂类型的子类型

类型可以用属性访问一样，直接访问。

``` TypeScript
interface Person {
    addr: {
        city: string
        street: string
        num: number
    }
}

interface newPerson {
    newAdd: string
    addr: Person['addr']
}

```

### 关联函数参数和返回值

利用`keyof`和泛型，以及查找类型，增强类型约束，增加IDE的代码提示。

``` TypeScript
interface API {
    '/user': { name: string }
    '/menu': { foods: string }
}

const get = <URL extends keyof API>(url: URL): Promise<API[URL]> => {
    return fetct(url).then(res => res.json())
}

get('/user') // 输入时会提示 /user和/menu
    .then(user => user.name) // 输入时会提示 name

```

`keyof`



## change log

- 2020/1/4 create document
