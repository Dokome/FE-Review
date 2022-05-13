## 实现TS自带高级类型

> TS 类型体操

#### Parameters

提取函数类型的参数类型。

```typescript
type Parameters<T extends (...args: any) => any> 
	= T extends (...args: infer P) => any
	? P : never
```

#### ReturnType

ReturnType 用于提取函数类型的返回值类型。

```typescript
type ReturnType<T extends (...args: any) => any> 
	= T extends (...args: any) => infer R
	? R: any
```

#### ContructorParameters

构造器类型和函数类型的区别就是可以被 new，Parameters 用于提取函数参数的类型，而 ConstructorParameters 用于提取构造器参数的类型。

> abstract 不加也行

```typescript
type ContructorParameters
	<T extends abstract new(...args: any) => any> 
	= T extends abstract new(...args: infer P) => any
	? P : never
```

#### InstanceType

提取了构造器参数的类型，自然也可以提取构造器返回值的类型，就是 InstanceType。

```typescript
type InstanceType
	<T extends abstract new(...args: any) => any> 
	= T extends abstract new(...args: any) => infer R
	? R : any
```

#### ThisParameterType 

函数里可以调用 this，这个 this 的类型也可以约束。

```typescript
type ThisParameterType<T> = T extends (this: infer U, ...args: any[]) => any 
    ? U : unknown
```

#### OmitThisParameter

提取出 this 的类型之后，自然可以构造一个新的，比如删除 this 的类型可以用 OmitThisParameter。

```typescript
type OmitThisParameter<T> = unknown extends ThisParameterType<T> 
	? T : T extends (...args: infer A) => infer R 
    	? (...args: A) => R 
   	: T
```

#### Partial

索引类型可以通过映射类型的语法做修改，比如把索引变为可选。

```typescript
type Partial<T> = {
    [P in keyof T]?: T[P]
}
```

#### Rquired

可以把索引变为可选，也同样可以去掉可选，也就是 Required 类型。

```typescript
type Partial<T> = {
    [P in keyof T]-?: T[P]
}
```

#### ReadOnly

将索引对象的属性变为只读。

```typescript
type Partial<T> = {
    readonly [P in keyof T]?: T[P]
}
```

#### Pick

映射类型的语法用于构造新的索引类型，在构造的过程中可以对索引和值做一些修改或过滤。

```typescript
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

#### Record 

Record 用于创建索引类型，传入 key 和值的类型。

```typescript
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

#### Exclude

当想从一个联合类型中去掉一部分类型时，可以用 Exclude 类型。

```typescript
type Exclude<T, U> = T extends U ? never : T;
```

#### Extract

可以过滤掉，自然也可以保留，Exclude 反过来就是 Extract，也就是取交集。

```typescript
type Exclude<T, U> = T extends U ? never : T;
```

#### Omit

我们知道了 Pick 可以取出索引类型的一部分索引构造成新的索引类型，那反过来就是去掉这部分索引构造成新的索引类型。

```typescript
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

#### Awaited

获取 Promise 最内层值的类型。

```typescript
type Exclude<T, U> = T extends U ? never : T;
```

#### NonNullable 

NonNullable 就是用于判断是否为非空类型，也就是不是 null 或者 undefined 的类型的，实现比较简单。

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
```

#### Awaited

获取 Promise 最内层值的类型。

```typescript
type Exclude<T, U> = T extends U ? never : T;
```

#### 其他

> Uppercase、Lowercase、Capitalize、Uncapitalize

这四个类型是分别实现大写、小写、首字母大写、去掉首字母大写的。

源码

```typescript
type Uppercase<S extends string> = intrinsic;
type Lowercase<S extends string> = intrinsic;
type Capitalize<S extends string> = intrinsic;
type Uncapitalize<S extends string> = intrinsic;
```

这个 intrinsic 是固有的意思，就像 js 里面的有的方法打印会显示 [native code] 一样。这部分类型不是在 ts 里实现的，而是编译过程中由 js 实现的。