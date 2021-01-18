---
title: 阅读type-fest源码
top: false
cover: false
toc: true
mathjax: true
date: 2021-01-18 14:38:34
password:
summary:
tags:
categories:
---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [type-fest翻译&源码](#type-fest%E7%BF%BB%E8%AF%91%E6%BA%90%E7%A0%81)
  - [TS基本操作符](#ts%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E7%AC%A6)
    - [extends（有条件类型）](#extends%E6%9C%89%E6%9D%A1%E4%BB%B6%E7%B1%BB%E5%9E%8B)
    - [infer（有条件类型中的类型推断）](#infer%E6%9C%89%E6%9D%A1%E4%BB%B6%E7%B1%BB%E5%9E%8B%E4%B8%AD%E7%9A%84%E7%B1%BB%E5%9E%8B%E6%8E%A8%E6%96%AD)
    - [keyof（获取key）](#keyof%E8%8E%B7%E5%8F%96key)
    - [typeof（获取类型）](#typeof%E8%8E%B7%E5%8F%96%E7%B1%BB%E5%9E%8B)
  - [TS自带类](#ts%E8%87%AA%E5%B8%A6%E7%B1%BB)
    - [Exclude（常用于去除联合类型中的一部分）](#exclude%E5%B8%B8%E7%94%A8%E4%BA%8E%E5%8E%BB%E9%99%A4%E8%81%94%E5%90%88%E7%B1%BB%E5%9E%8B%E4%B8%AD%E7%9A%84%E4%B8%80%E9%83%A8%E5%88%86)
    - [Extract](#extract)
    - [其他自带类（Pick，Record，Partial等）](#%E5%85%B6%E4%BB%96%E8%87%AA%E5%B8%A6%E7%B1%BBpickrecordpartial%E7%AD%89)
  - [type-fest工具类](#type-fest%E5%B7%A5%E5%85%B7%E7%B1%BB)
    - [Except（丢弃object type的某些keys，生成一个新的type。是更严格的Omit）](#except%E4%B8%A2%E5%BC%83object-type%E7%9A%84%E6%9F%90%E4%BA%9Bkeys%E7%94%9F%E6%88%90%E4%B8%80%E4%B8%AA%E6%96%B0%E7%9A%84type%E6%98%AF%E6%9B%B4%E4%B8%A5%E6%A0%BC%E7%9A%84omit)
    - [Mutable（将原类型中的readonly属性移除，和Readonly作用相反）](#mutable%E5%B0%86%E5%8E%9F%E7%B1%BB%E5%9E%8B%E4%B8%AD%E7%9A%84readonly%E5%B1%9E%E6%80%A7%E7%A7%BB%E9%99%A4%E5%92%8Creadonly%E4%BD%9C%E7%94%A8%E7%9B%B8%E5%8F%8D)
    - [Merge（FirstType与SecondType的融合，SecondType会覆盖重复的部分）](#mergefirsttype%E4%B8%8Esecondtype%E7%9A%84%E8%9E%8D%E5%90%88secondtype%E4%BC%9A%E8%A6%86%E7%9B%96%E9%87%8D%E5%A4%8D%E7%9A%84%E9%83%A8%E5%88%86)
    - [MergeExclusive（限定更紧的type联合类型）](#mergeexclusive%E9%99%90%E5%AE%9A%E6%9B%B4%E7%B4%A7%E7%9A%84type%E8%81%94%E5%90%88%E7%B1%BB%E5%9E%8B)
    - [RequireAtLeastOne（根据key，使原类型中属性，任意且至少一个变为必选，剩余的变为可选）](#requireatleastone%E6%A0%B9%E6%8D%AEkey%E4%BD%BF%E5%8E%9F%E7%B1%BB%E5%9E%8B%E4%B8%AD%E5%B1%9E%E6%80%A7%E4%BB%BB%E6%84%8F%E4%B8%94%E8%87%B3%E5%B0%91%E4%B8%80%E4%B8%AA%E5%8F%98%E4%B8%BA%E5%BF%85%E9%80%89%E5%89%A9%E4%BD%99%E7%9A%84%E5%8F%98%E4%B8%BA%E5%8F%AF%E9%80%89)
    - [RequireExactlyOne（根据key，使原类型中属性，任意且只有一个变为必选，剩余的变为不可选）](#requireexactlyone%E6%A0%B9%E6%8D%AEkey%E4%BD%BF%E5%8E%9F%E7%B1%BB%E5%9E%8B%E4%B8%AD%E5%B1%9E%E6%80%A7%E4%BB%BB%E6%84%8F%E4%B8%94%E5%8F%AA%E6%9C%89%E4%B8%80%E4%B8%AA%E5%8F%98%E4%B8%BA%E5%BF%85%E9%80%89%E5%89%A9%E4%BD%99%E7%9A%84%E5%8F%98%E4%B8%BA%E4%B8%8D%E5%8F%AF%E9%80%89)
    - [PartialDeep（深度Partial，将基本数据类型、Map、Set等嵌套数据结构进行Partial操作）](#partialdeep%E6%B7%B1%E5%BA%A6partial%E5%B0%86%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8Bmapset%E7%AD%89%E5%B5%8C%E5%A5%97%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E8%BF%9B%E8%A1%8Cpartial%E6%93%8D%E4%BD%9C)
    - [ReadonlyDeep（深度Readonly，将基本数据类型、Map、Set等嵌套数据结构进行Readonly操作）](#readonlydeep%E6%B7%B1%E5%BA%A6readonly%E5%B0%86%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8Bmapset%E7%AD%89%E5%B5%8C%E5%A5%97%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E8%BF%9B%E8%A1%8Creadonly%E6%93%8D%E4%BD%9C)
    - [LiteralUnion（兼容 基本数据类型与字面量类型的联合数据类型 IDE自动提示补全）](#literalunion%E5%85%BC%E5%AE%B9-%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E4%B8%8E%E5%AD%97%E9%9D%A2%E9%87%8F%E7%B1%BB%E5%9E%8B%E7%9A%84%E8%81%94%E5%90%88%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B-ide%E8%87%AA%E5%8A%A8%E6%8F%90%E7%A4%BA%E8%A1%A5%E5%85%A8)
    - [Promisable（代表value以及被PromiseLike包裹的value的type）](#promisable%E4%BB%A3%E8%A1%A8value%E4%BB%A5%E5%8F%8A%E8%A2%ABpromiselike%E5%8C%85%E8%A3%B9%E7%9A%84value%E7%9A%84type)
    - [Opaque（不透明的类型）](#opaque%E4%B8%8D%E9%80%8F%E6%98%8E%E7%9A%84%E7%B1%BB%E5%9E%8B)
    - [SetOptional（有选择的Partial）](#setoptional%E6%9C%89%E9%80%89%E6%8B%A9%E7%9A%84partial)
    - [SomeRequired（SetOptional的姊妹类型，使type的部分key变得必须）](#somerequiredsetoptional%E7%9A%84%E5%A7%8A%E5%A6%B9%E7%B1%BB%E5%9E%8B%E4%BD%BFtype%E7%9A%84%E9%83%A8%E5%88%86key%E5%8F%98%E5%BE%97%E5%BF%85%E9%A1%BB)
    - [ValueOf（接受一个类型范型T，返回T的value联合类型）](#valueof%E6%8E%A5%E5%8F%97%E4%B8%80%E4%B8%AA%E7%B1%BB%E5%9E%8B%E8%8C%83%E5%9E%8Bt%E8%BF%94%E5%9B%9Et%E7%9A%84value%E8%81%94%E5%90%88%E7%B1%BB%E5%9E%8B)
    - [PromiseValue(获取promise值的type)](#promisevalue%E8%8E%B7%E5%8F%96promise%E5%80%BC%E7%9A%84type)
    - [AsyncReturnType（获取异步函数返回值的type）](#asyncreturntype%E8%8E%B7%E5%8F%96%E5%BC%82%E6%AD%A5%E5%87%BD%E6%95%B0%E8%BF%94%E5%9B%9E%E5%80%BC%E7%9A%84type)
    - [ConditionalKeys（返回符合条件的key）](#conditionalkeys%E8%BF%94%E5%9B%9E%E7%AC%A6%E5%90%88%E6%9D%A1%E4%BB%B6%E7%9A%84key)
    - [ConditionalPick（返回符合条件的key组成的type）](#conditionalpick%E8%BF%94%E5%9B%9E%E7%AC%A6%E5%90%88%E6%9D%A1%E4%BB%B6%E7%9A%84key%E7%BB%84%E6%88%90%E7%9A%84type)
    - [ConditionalExcept（返回不符合条件的key组成的type）](#conditionalexcept%E8%BF%94%E5%9B%9E%E4%B8%8D%E7%AC%A6%E5%90%88%E6%9D%A1%E4%BB%B6%E7%9A%84key%E7%BB%84%E6%88%90%E7%9A%84type)
    - [UnionToIntersection（将联合类型变为交叉类型）](#uniontointersection%E5%B0%86%E8%81%94%E5%90%88%E7%B1%BB%E5%9E%8B%E5%8F%98%E4%B8%BA%E4%BA%A4%E5%8F%89%E7%B1%BB%E5%9E%8B)
    - [Stringified（将type中的类型全部变为string）](#stringified%E5%B0%86type%E4%B8%AD%E7%9A%84%E7%B1%BB%E5%9E%8B%E5%85%A8%E9%83%A8%E5%8F%98%E4%B8%BAstring)
    - [FixedLengthArray（长度不可变的数组）](#fixedlengtharray%E9%95%BF%E5%BA%A6%E4%B8%8D%E5%8F%AF%E5%8F%98%E7%9A%84%E6%95%B0%E7%BB%84)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# type-fest翻译&源码
type-fest是一个typeScript类型的集合

## TS基本操作符
### extends（有条件类型）
typescript 2.8引入了条件类型关键字: extends
```
    // extends 关键字既可以来扩展已有的类型，也可以对类型进行条件限定，进行条件限定时，用法如下：
    // 若T能够赋值给U，那么类型是X，否则为Y，具体实现可以看参考阅读
    T extends U ? X : Y
```
参考阅读：    
[typeScript有条件类型](https://www.tslang.cn/docs/release-notes/typescript-2.8.html)   

### infer（有条件类型中的类型推断）
在有条件类型的extends子语句中，允许出现infer声明，它会引入一个待推断的类型变量。进行占位，对类型变量进行命名
```
    // 下面代码会提取函数类型的返回值类型
    type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

### keyof（获取key）
获取类型key的联合类型

### typeof（获取类型）
获取类型的字面量类型

## TS自带类
### Exclude（常用于去除联合类型中的一部分）
用法：type A = Exclude<T, U>;
输入：T和U这两个范型
输出：一个新的type

```
    // 实现
    export type Exclude<T, U> = T extends U ? never: T;

    // 例子
    type A = 1 | 2;
    type B = 2;
    type C = Exclude<A, B>;
    //=> 1;
```
但是根据实现以及例子来看，type A extends type B应该为false，那么应该返回范型T（即type A本身），但结果返回了type A的子集 => 1
即，
预期：1 ｜ 2 
结果：1
这是为什么呢？
这是因为typeScript中有一种概念叫"naked type parameter"（裸露的类型）它代表着类型没有被其他type包裹
其中的区别主要体现在：
naked type parameter会将范型拆分一个联合类型，联合类型中的每个成员都会受到影响，最终返回一个最终结果
因为 1 | 2，是裸露的类型，所以会依次对比并返回，但如果是非裸露的类型，就不会依次对比（例如被数组包裹）
```
    // naked type parameter（裸露的类型）
    type NakedUsage<T> = T extends boolean ? "YES" : "NO"
    // non nakes（被数组包裹的非裸露类型）
    type WrappedUsage<T> = [T] extends [boolean] ? "YES" : "NO"; // wrapped in a tuple
    
    type Distributed = NakedUsage<number | boolean > // = NakedUsage<number> | NakedUsage<boolean> =  "NO" | "YES" 
    type NotDistributed = WrappedUsage<number | boolean > // "NO"    
    type NotDistributed2 = WrappedUsage<boolean > // "YES"
```

### Extract
与Exclude相对的是Extract
```
    type Extract<T, U> = T extends U ? T : never
```

参考阅读：   
[typescript: what is a “naked type parameter”](https://stackoverflow.com/questions/51651499/typescript-what-is-a-naked-type-parameter/51651684#51651684)  
[typescript 2.8 分布式有条件类型](https://www.tslang.cn/docs/release-notes/typescript-2.8.html)

### 其他自带类（Pick，Record，Partial等）
Pick（根据key返回一个子类型）
Omit（Pick与Exclude的组合，根据key舍弃类型，剩余的key组成一个子类型返回）
Record（根据key改写类型，并返回）
Partial（范型接受一个类型，将类型中的key变得可以缺失）
Required（范型接受一个类型，将类型中的key变得必须存在）
Readonly（范型接受一个类型，将类型中的key变得只读）

## type-fest工具类
### Except（丢弃object type的某些keys，生成一个新的type。是更严格的Omit）
```
    // 实现
    export type Except<ObjectType, KeysType extends keyof ObjectType> =
    Pick<ObjectType, Exclude<keyof ObjectType, KeysType>>;
    
    // 例子
    type Foo = {
    	a: number;
    	b: string;
    	c: boolean;
    };
    
    type FooWithoutA = Except<Foo, 'a' | 'c'>;
    //=> {b: string};
```
对比实现可以发现，Except对于key的限制更紧，必须是keyof ObjectType的子集。而Omit则没有限制

### Mutable（将原类型中的readonly属性移除，和Readonly作用相反）
```
    // 实现
    export type Mutable<ObjectType> = {
    	-readonly [KeyType in keyof ObjectType]: ObjectType[KeyType];
    };

    // 例子
    type Foo = {
    	readonly a: number;
    	readonly b: string;
    };
    
    const mutableFoo: Mutable<Foo> = {a: 1, b: '2'};
    mutableFoo.a = 3;
```

### Merge（FirstType与SecondType的融合，SecondType会覆盖重复的部分）
```
    // 实现
    export type Merge<FirstType, SecondType> = 
    Except<FirstType, Extract<keyof FirstType, keyof SecondType>> & SecondType;

    // 例子
    type Foo = {
    	a: number;
    	b: string;
    };
    
    type Bar = {
    	b: number;
    };
    
    const ab: Merge<Foo, Bar> = {a: 1, b: 2};
```

### MergeExclusive（限定更紧的type联合类型）
如果FirstType和SecondType都是基本数据类型，则返回或的联合数据类型
否则使用FirstType或SecondType的联合类型
```
    // 内部实现Without（将FirstType的特有属性，变成never，构成一个子类型返回）（为了方便移除特有属性）
    type Without<FirstType, SecondType> = {[KeyType in Exclude<keyof FirstType, keyof SecondType>]?: never};

    // MergeExclusive实现
    // 注意(FirstType | SecondType)这里是一个整体、联合类型
    export type MergeExclusive<FirstType, SecondType> =
    	(FirstType | SecondType) extends object ?
    		(Without<FirstType, SecondType> & SecondType) | (Without<SecondType, FirstType> & FirstType) :
    		FirstType | SecondType;

    // 例子
    interface ExclusiveVariation1 {
    	exclusive1: boolean;
    }
    
    interface ExclusiveVariation2 {
    	exclusive2: string;
    }

    type ExclusiveOptions = MergeExclusive<ExclusiveVariation1, ExclusiveVariation2>;
    // never & ExclusiveVariation2 | nerver & ExclusiveVariation1
    let exclusiveOptions: ExclusiveOptions;

    exclusiveOptions = {exclusive1: true};
    //=> Works
    exclusiveOptions = {exclusive2: 'hi'};
    //=> Works
    exclusiveOptions = {exclusive1: true, exclusive2: 'hi'};
```
对比实现可发现，相比于普通的联合类型，MergeExclusive限定更紧，不能同时持有2个类型

### RequireAtLeastOne（根据key，使原类型中属性，任意且至少一个变为必选，剩余的变为可选）
```
    // 实现
    // 第一个参数为范型T，第二个参数是key的子集，如果不填则默认为全部的key
    export type RequireAtLeastOne<
    	ObjectType,
    	KeysType extends keyof ObjectType = keyof ObjectTypef
    > = {
        // 根据key使得一个key变得必选（个人感觉 -? 在这里没有用，有知道的hxd可以评论区讨论一下）
    	[Key in KeysType]-?: Required<Pick<ObjectType, Key>> &
        // 其他key变得可选
    	Partial<Pick<ObjectType, Exclude<KeysType, Key>>>;
    // 将type对象合并为联合类型返回（这样做才能实现任意一个key）
    }[KeysType] &
    	// 未被传入的其他的key保持不变
    	Except<ObjectType, KeysType>;

    // 例子
    interface User {
      name: string;
      age: number;
      gender: string;
    }
    // => 
    //  {
    //    name: string;
    //    age: number;
    //    gender?: string;
    //  } |
    //  {
    //    name: string;
    //    age: number;
    //    gender?: string;
    //  };
    type Test = RequireAtLeastOne<User, 'name' | 'age'>
    // 错误，name或age应该至少存在一个
    const error: Test = {
      gender: 'male',
    };
    // 正确，存在name
    const correct1: Test = {
      gender: 'male',
      name: 'correct',
    };
    // 正确
    const correct2: Test = {
      gender: 'male',
      age: 1,
      name: 'correct'
    };
```

### RequireExactlyOne（根据key，使原类型中属性，任意且只有一个变为必选，剩余的变为不可选）
```
    // 实现
    // typeScript >=3.5 _Omit将被移除（ts3.5 内部实现了Omit）
    type _Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
    export type RequireExactlyOne<ObjectType, KeysType extends keyof ObjectType = keyof ObjectType> =
    	{[Key in KeysType]: (
    		Required<Pick<ObjectType, Key>> &
    		Partial<Record<Exclude<KeysType, Key>, never>>
    	)}[KeysType] & _Omit<ObjectType, KeysType>;
    // 例子
```

### PartialDeep（深度Partial，将基本数据类型、Map、Set等嵌套数据结构进行Partial操作）
```
    //实现
    export type PartialDeep<T> = T extends Primitive
    	? Partial<T>
    	: T extends Map<infer KeyType, infer ValueType>
    	? PartialMapDeep<KeyType, ValueType>
    	: T extends Set<infer ItemType>
    	? PartialSetDeep<ItemType>
    	: T extends ReadonlyMap<infer KeyType, infer ValueType>
    	? PartialReadonlyMapDeep<KeyType, ValueType>
    	: T extends ReadonlySet<infer ItemType>
    	? PartialReadonlySetDeep<ItemType>
    	: T extends ((...arguments: any[]) => unknown)
    	? T | undefined
    	: T extends object
    	? PartialObjectDeep<T>
    	: unknown;
    //例子
```

### ReadonlyDeep（深度Readonly，将基本数据类型、Map、Set等嵌套数据结构进行Readonly操作）
```
    // ...
```

### LiteralUnion（兼容 基本数据类型与字面量类型的联合数据类型 IDE自动提示补全）
```
    // 实现
    export type LiteralUnion<
    	LiteralType,
    	BaseType extends Primitive
    > = LiteralType | (BaseType & {_?: never});
    // 例子（webstorm不提示，然而vscode适用）
    type Pet = 'dog' | 'cat' | string;
    const pet: Pet = ''; // 不会自动提示dog和cat

    type Pet2 = LiteralUnion<'dog' | 'cat', string>;
    const pet: Pet2 = ''; // 会自动提示dog和cat
```

### Promisable（代表value以及被PromiseLike包裹的value的type）
```
    // 实现
    export type Promisable<T> = T | PromiseLike<T>;
    // PromiseLike是ts定义的interface，定义了面向Promise的结构
    // 例子
    async function logger(getLogEntry: () => Promisable<string>): Promise<void> {
        const entry = await getLogEntry();
        console.log(entry);
    }
    
    logger(() => 'foo');
    logger(() => Promise.resolve('bar'));
```

### Opaque（不透明的类型）
感觉不太实用，加Token用来区分基本数据类型，不同Token代表不同的类型，相同的Token则可以相互赋值
（例如字符串代表的含义不同？名字和住址不可以相互赋值，但英文名和中文可以相互赋值）
```
    // 实现
    export type Opaque<Type, Token = unknown> = Type & {readonly __opaque__: Token};
```

### SetOptional（有选择的Partial）
```
    // 实现
    export type SetOptional<BaseType, Keys extends keyof BaseType> =
    	Simplify<
    		// 必选的key组成的type
    		Except<BaseType, Keys> &
    		// 可选的key组成的type
    		Partial<Pick<BaseType, Keys>>
    	>;
```

### SomeRequired（SetOptional的姊妹类型，使type的部分key变得必须）
```
    // 实现
    export type SetRequired<BaseType, Keys extends keyof BaseType> =
    	Simplify<
    		Except<BaseType, Keys> &
    		Required<Pick<BaseType, Keys>>
    	>;
```

### ValueOf（接受一个类型范型T，返回T的value联合类型）
```
    // 实现
    export type ValueOf<ObjectType, ValueType extends keyof ObjectType = keyof ObjectType> = ObjectType[ValueType];
```

### PromiseValue(获取promise值的type)
```
    // 实现
    export type PromiseValue<PromiseType, Otherwise = PromiseType> = PromiseType extends Promise<infer Value>
    	? { 0: PromiseValue<Value>; 1: Value }[PromiseType extends Promise<unknown> ? 0 : 1]
    	: Otherwise;
```

### AsyncReturnType（获取异步函数返回值的type）
```
    // 实现
    // ts内部实现ReturnType（获取函数返回值类型）
    type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

    export type AsyncReturnType<Target extends AsyncFunction> = PromiseValue<ReturnType<Target>>;
```

### ConditionalKeys（返回符合条件的key）
```
    // 实现
    // ts内部实现NonNullable（排除null和undefined）
    type NonNullable<T> = T extends null | undefined ? never : T;

    export type ConditionalKeys<Base, Condition> = NonNullable<
    	{
    		// Map through all the keys of the given base type.
    		[Key in keyof Base]:
    			// 根据Condition筛选
    			Base[Key] extends Condition
    				? Key
    				: never;
        // 将结果type转为union type
    	}[keyof Base]
    >;
    // 例子
    interface Example {
    	a: string;
    	b: string | number;
    	c?: string;
    	d: {};
    }
    type StringKeysOnly = ConditionalKeys<Example, string>;
    // 非空且类型为string的只有a  reuslt => 'a'
```

### ConditionalPick（返回符合条件的key组成的type）
```
    // 实现
    export type ConditionalPick<Base, Condition> = Pick<
    	Base,
    	ConditionalKeys<Base, Condition>
    >;
```

### ConditionalExcept（返回不符合条件的key组成的type）
```
    // 实现
    export type ConditionalExcept<Base, Condition> = Except<
    	Base,
    	ConditionalKeys<Base, Condition>
    >;
```

### UnionToIntersection（将联合类型变为交叉类型）
```
    // 实现
    export type UnionToIntersection<Union> = (
        // extends unknown返回总是true，构造一个联合的函数类型，进而返回一个交叉类型
    	Union extends unknown
    		? (distributedUnion: Union) => void
    		: never
    	) extends ((mergedIntersection: infer Intersection) => void)
            // 将联合类型转为交叉类型
    		? Intersection
    		: never;
```

### Stringified（将type中的类型全部变为string）
```
    // 实现
    export type Stringified<ObjectType> = {[KeyType in keyof ObjectType]: string};
```

### FixedLengthArray（长度不可变的数组）
```
    // 实现
    // 需要排除的方法
    type ArrayLengthMutationKeys = 'splice' | 'push' | 'pop' | 'shift' | 'unshift';
    export type FixedLengthArray<Element, Length extends number, ArrayPrototype = [Element, ...Element[]]> = Pick<
    	ArrayPrototype,
    	Exclude<keyof ArrayPrototype, ArrayLengthMutationKeys>
    > & {
    	[index: number]: Element;
    	[Symbol.iterator]: () => IterableIterator<Element>;
    	readonly length: Length;
    };
```

