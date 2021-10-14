# TS 实用工具类型

## `Partial<Type>`

构造类型 Type，将 Type 的所有属性设置为可选。该类型将返回表示输入类型的所有字类型。

> Constructs a type with all properties of `Type` set to optional. This utility will return a type that represents all subsets of a given type.

```typescript
interface todo {
  title: string;
  description: string;
}
function updateTodo(todo:Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo,...fieldsToUpdate };
}

const todo1 = {
  title: 'work',
  description: 'every day'
};

const todo2 = updateTodo(todo1,{
  description: 'not every day'
})
```

## `Required<Type>`

与 `Partial` 相反。将所有属性设置为必选项。

> Constructs a type consisting of all properties of `Type` set to required. The opposite of [`Partial`](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype).

```typescript
interface Props {
  a?: number;
  b?: string;
}

const obj: Props = {a: 6};

const obj2: Required<Props> = {a:3};

>error:Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```

## `Readonly<Type>`

构造类型 Type ，将 Type 的所有属性设置为 `readonly`，意味着不能给属性重新赋值。

> Constructs a type with all properties of `Type` set to `readonly`, meaning the properties of the constructed type cannot be reassigned.

```typescript
interface Todo {
  title: string;
}

const todo: Readonly<Todo> = {
	title: 'Work',
}

todo.title = 'play';

>error:Cannot assign to 'title' because it is a read-only property.
```

将尝试冻结`freeze` 一个对象时，此工具类型非常有帮助。

> This utility is useful for representing assignment expressions that will fail at runtime (i.e. when attempting to reassign properties of a [frozen object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)).

## `Record<Keys,Type>`

构造一个类型，其属性名的类型为`Keys`，属性值的类型为`Type`。这个工具可用来将某个类型的属性映射到另一个类型上。

> Constructs an object type whose property keys are `Keys` and whose property values are `Type`. This utility can be used to map the properties of a type to another type.

```typescript
interface CatInfo {
  age: number;
  breed: string;
}

type CatName = 'miffy' | 'boris' | 'mordred';

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: 'Persian'},
  boris: { age: 1,breed: 'Maine Coon'},
  mordred: { age: 16, breed: "British Shorthair" },
}
```

## `Pick<Type, Keys>` 

从类型`Type`中挑选部分属性`Keys`来构造类型。

> Constructs a type by picking the set of properties `Keys` (string literal or union of string literals) from `Type`.

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

todo;
// const todo: TodoPreview
```

## `Omit<Type,Keys>`

从类型`Type`中获取所有属性，然后从中剔除`Keys`属性后构造一个类型。

> Constructs a type by picking all properties from `Type` and then removing `Keys` (string literal or union of string literals).

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number
}

type TodoPreview = Omit<Todo, 'description'>;

const todo: TodoPreview = {
  title: 'clean room',
  completed: false;
  createdAt: new Date().getTime()
}

todo;
// TodoPreview

type TodoInfo = Omit<Todo,'completed' | 'createdAt'>;

const todoInfo: TodoInfo = {
  title: 'pick up kids',
  description: 'kindergarten clased at 5pm'
}

```

## `Exclude<Type, ExcludedUnion>` 

从类型 `Type` 中剔除所有可以赋值给 `ExcludedUnion` 的属性，然后构造一个类型。

> Constructs a type by excluding from `Type` all union members that are assignable to `ExcludedUnion`.

```typescript
type T0 = Exclude<'a' | 'b' | 'c', 'a'>;
// type T0 = 'b' | 'c';

type T1 = Exclude<'a' | 'b' 'c', 'a' | 'b'>;
// type T1 = 'c';
                  
type T2 = Exclude<string | number | (()=> void), Function>;
// type T2 = string | number;
```

## `Extract<Type, Union>` 

从类型`Type`中提取所有可以赋值给`Union`的类型，然后构造一个类型。

> Constructs a type by extracting from `Type` all union members that are assignable to `Union`.

```typescript
type T0 = Extract<'a' | 'b' | 'c', 'a' | 'f'>;
// type T0 = 'a';

type T1 = Extract<string | number | (()=> void), Function>;
// type T1 = ()=> void
```

## `NonNullable<Type>`

从类型`T`中剔除`null`和`undefined`，然后构造一个类型。

> Constructs a type by excluding `null` and `undefined` from `Type`.

```typescript
type T0 = NonNullable<string | number | undefined>;
// type T0 = string | number;
type T1 = NonNullable<string[] | null | undefined>;
// type T1 = string[]
```

## `Parameters<Type>`

从函数类型Type的参数中使用的类型构造一个元组类型。

> Constructs a tuple type from the types used in the parameters of a function type 'Type'

```typescript
declare function f1(arg:{a:number;b:string}):void;
type T0 = Parameters<()=>string>;
// type T0 = []
type T1 = Parameters<(s:string)=>void>;
// type T1 = [s:string]
type T2 = Parameters<<T>(arg:T)=>T>;
// type T2 = [arg:unknown]
type T3 = Parameters<typeof f1>;
// type T3 = [arg: {a:number;b:string}]
type T4 = Parameters<any>
// type T4 = unknown[];
type T5 = Parameters<never>;
// type T5 = never;
type T6 = Parameters<string>;
//Type 'string' does not satisfy the constraint '(...args: any) => any'.   
// type T6 = never
type T7 = Parameters<Function>;
//Type 'Function' does not satisfy the constraint '(...args: any) => any'.
//  Type 'Function' provides no match for the signature '(...args: any): any'.
// type T7 = never
```

## `ConstructorParameters<Type>`

从构造函数的类型构造元组或数组类型。它会生成具有所有参数类型的元组类型（如果Type不是函数，则该类型为 `never` ）。

> Constructs a tuple or array type from the types of a constructor function type. It produces a tuple type with all the parameter types (or the type `never` if `Type` is not a function).

```typescript
type T0 = ConstructorParameters<ErrorConstructor>
// type T0 = [message?:string]

type T1 = ConstructorParameters<FunctionConstrunctor>;
// type T1 = string[]

type T2 = ConstructorParameters<RegExpConstructor>;
// type T2 = [pattern:string | RegExp, flags?:string]

type T3 = ConstructorParameters<any>;
// type T3 = unknown[];

type T4 = ConstructorParameters<Function>;
// Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
//  Type 'Function' provides no match for the signature 'new (...args: any): any'.
// type T4 = never
```

## `ReturnType<Type>`

由函数类型 `Type` 的返回值类型构造一个类型。

> Constructs a type consisting of the return type of function `Type`.

```typescript
declare function f1(): { a: number; b: string};

type T0 = ReturnType<()=>string>;
// type T0 = string;

type T1 = ReturnType<(s:string) => void>;
// type T1 = void;

type T2 = ReturnType<<T>()=> T>;
// type T2 = unknown;

type T3 =  ReturnType<<T extends U,U extends number[]>() => T>;
// type T3 = number[];

type T4 =  ReturnType<typeof f1>;
// type T4 = {a:number; b:string};

type T5 =  ReturnType<any>;
// type T5 = any;
                      
type T6 =  ReturnType<never>;
// type T6 = never;

type T7 =  ReturnType<string>;
// Type 'string' does not satisfy the constraint '(...args: any) => any'.
// type T7 = any
                      
type T8 = ReturnType<Function>
// Type 'Function' does not satisfy the constraint '(...args: any) => any'.
//  Type 'Function' provides no match for the signature '(...args: any): any'.
// type T8 = any
```

## `InstanceType<Type>`

由构造函数类型 `Type` 的实例类型构造一个类型。

> Constructs a type consisting of the instance type of a constructor function in `Type`.

```typescript
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>;
// type T0 = C;
                       
type T1 = InstanceType<any>;
// type T1 = any;
                       
type T2 = InstanceType<never>;
// type T2 = never
                       
type T3 = InstanceType<string>;
// Type 'string' does not satisfy the constraint 'abstract new (...args: any) => any'.
// type T3 = any
                       
type T4 = InstanceType<Function>;
//Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
//  Type 'Function' provides no match for the signature 'new (...args: any): any'.   
// type T4 = any
```

## `ThisParameterType<Type>`

提取函数类型的 'this' 参数的类型，如果函数类型没有 this 参数，则提取此函数的类型

> Extracts the type of the [this](https://www.typescriptlang.org/docs/handbook/functions.html#this-parameters) parameter for a function type, or [unknown](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type) if the function type has no `this` parameter.

```typescript
function toHex(this: Number) {
  return this.toString(16);
}

type T0 = ThisParameterType<typeof toHex>
// type T0 = number
                            
function numberToString(n: T0) {
  return toHex.apply(n);
}
```

## `OmitThisParameter<Type>`

Removes the [`this`](https://www.typescriptlang.org/docs/handbook/functions.html#this-parameters) parameter from `Type`. If `Type` has no explicitly declared `this` parameter, the result is simply `Type`. Otherwise, a new function type with no `this` parameter is created from `Type`. Generics are erased and only the last overload signature is propagated into the new function type.

```typescript
function toHex(this: Number) {
  return this.toString(16);
}

type T0 =  OmitThisParameter<typeof toHex>;
// type T0 = () => string
const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);

console.log(fiveToHex());
```

## `ThisType<Type>`

这个工具不会返回一个转换后的类型。它做为上下文的`this`类型的一个标记。注意，若想使用此类型，必须启用`--noImplicitThis`。

```typescript
/ Compile with --noImplicitThis

type ObjectDescriptor<D, M> = {
    data?: D;
    methods?: M & ThisType<D & M>;  // Type of 'this' in methods is D & M
}

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
    let data: object = desc.data || {};
    let methods: object = desc.methods || {};
    return { ...data, ...methods } as D & M;
}

let obj = makeObject({
    data: { x: 0, y: 0 },
    methods: {
        moveBy(dx: number, dy: number) {
            this.x += dx;  // Strongly typed this
            this.y += dy;  // Strongly typed this
        }
    }
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

上面例子中，`makeObject`参数里的`methods`对象具有一个上下文类型`ThisType<D & M>`，因此`methods`对象的方法里`this`的类型为`{ x: number, y: number } & { moveBy(dx: number, dy: number): number }`。

在`lib.d.ts`里，`ThisType<T>`标识接口是个简单的空接口声明。除了在被识别为对象字面量的上下文类型之外，这个接口与一般的空接口没有什么不同。