# TypeScript Guide

- [TypeScript Guide](#typescript-guide)
  - [Simple Types](#simple-types)
    - [Type Assignment](#type-assignment)
      - [Explicit Type](#explicit-type)
      - [Implicit Type](#implicit-type)
    - [Error In Type Assignment](#error-in-type-assignment)
    - [Unable to Infer](#unable-to-infer)
  - [Special Types](#special-types)
    - [Type: `any`](#type-any)
    - [Type: `unknown`](#type-unknown)
    - [Type: `never`](#type-never)
    - [Type: `undefined` \& `null`](#type-undefined--null)
  - [Arrays](#arrays)
    - [Readonly](#readonly)
    - [Type Inference](#type-inference)
  - [Tuples](#tuples)
    - [Readonly Tuple](#readonly-tuple)
    - [Named Tuples](#named-tuples)
    - [Destructuring Tuples](#destructuring-tuples)
  - [Object Types](#object-types)
    - [Type Inference](#type-inference-1)
    - [Optional Properties](#optional-properties)
    - [Index Signatures](#index-signatures)
  - [Enums](#enums)
    - [Numeric Enums](#numeric-enums)
      - [Numeric Enums - Default](#numeric-enums---default)
      - [Numeric Enums - Initialized](#numeric-enums---initialized)
      - [Numeric Enums - Fully Initialized](#numeric-enums---fully-initialized)
    - [String Enums](#string-enums)
  - [Type Aliases and Interfaces](#type-aliases-and-interfaces)
    - [Type Aliases](#type-aliases)
    - [Interfaces](#interfaces)
  - [Union Types](#union-types)
    - [Union Type Errors](#union-type-errors)
  - [Functions](#functions)
    - [Return Type](#return-type)
    - [Void Return Type](#void-return-type)
    - [Parameters](#parameters)
      - [Optional Parameters](#optional-parameters)
      - [Default Parameters](#default-parameters)
      - [Named Parameters](#named-parameters)
      - [Rest Parameters](#rest-parameters)
    - [Type Alias](#type-alias)
  - [Casting](#casting)
    - [Casting with `as`](#casting-with-as)
    - [Casting with `<>`](#casting-with-)
    - [Force casting](#force-casting)


## Simple Types

Type เบื้องต้น มี 3 type ได้แก่
- `boolean`: เป็น true หรือ false
- `number`: จำนวนทั้งหมด เป็นได้ทั้ง int หรือ float
- `string`: text ทุกแบบ

TypeScript เวอร์ชั่นหลังๆจะมีเพิ่มอีก 2 type คือ
- `bigint`: คล้าย `number` แต่ใหญ่กว่า
- `symbol`: ใช้ประกาศค่า global

### Type Assignment

มี 2 แบบ

#### Explicit Type

คือการประกาศ type มาเลย

```ts
let firstName: string = "Dylan";
```
#### Implicit Type

คือการไม่ประกาศ type อะไรเลย แล้วให้ TypeScript เดาเอาเอง
(** เราเรียกการเดาของ ts ว่า infer)

```ts
let firstName = "Dylan";
console.log(typeof firstName); // string
```

### Error In Type Assignment

จะ error ถ้า define ตัวแปรไม่ตรงกับ type

```ts
let firstName: string = "Dylan";
firstName = 33; // attempts to re-assign the value to a different type
console.log(firstName); // Type 'number' is not assignable to type 'string'
```

หรือ ต่อให้ implicit ก็ไม่รอด

```ts
let firstName = "Dylan"; // inferred to type string
firstName = 33; // attempts to re-assign the value to a different type
console.log(firstName); // Type 'number' is not assignable to type 'string'
```

### Unable to Infer

บางครั้ง ts ก็ infer ไม่ได้ว่าเป็น type อะไร อย่างในตัวอย่าง มันจะถือว่าเป็น `any` type แทน

```ts
// Implicit any as JSON.parse doesn't know what type of data it returns so it can be "any" thing... 
const json = JSON.parse("55");
// Most expect json to be an object, but it can be a string or a number like this example
console.log(typeof json); // number
```

## Special Types

### Type: `any`

จะไม่มีการ check type และสามารถประกาศค่าเป็น type ไหนก็ได้

```ts
let u = true;
u = "string";
console.log(Math.round(u)); // Error: Argument of type 'boolean' is not assignable to parameter of type 'number'
```

ตัวแปร u ไม่มีการประกาศ `any` ทำให้โดน infer ว่าเป็น boolean

```ts
let v: any = true;
v = "string";
console.log(Math.round(v)); // no error as it can be "any" type
```

ส่วน v มีการประกาศ `any` ทำให้สามารถเปลี่ยนค่าเป็น type อะไรก็ได้

### Type: `unknown`

คล้ายๆกับ `any` แต่ปลอดภัยกว่า

```ts
let w: unknown = 1;
w = "string"; // no error

w = { 
  runANonExistentMethod: () => {
    console.log("I think therefore I am");
  }
} as { runANonExistentMethod: () => void }

// w.runANonExistentMethod(); // Error: Object is of type 'unknown'.

if(typeof w === 'object' && w !== null) {
  (w as { runANonExistentMethod: Function }).runANonExistentMethod();
} // I think therefore I am
```

จากตัวอย่าง เราสามารถประกาศ type `unknown` ก่อน ถ้าไม่ทราบว่าเป็น type อะไร แล้วค่อยประกาศทีหลัง โดยใช้ `as` keyword


### Type: `never`

ประกาศค่าอะไรก็ error หมด (ไม่ค่อยมีใช้)

```ts
let x: never = true; // Error: Type 'boolean' is not assignable to type 'never'.
x: never = 1; // Error: Type 'number' is not assignable to type 'never'.
x: never = '1'; // Error: Type 'string' is not assignable to type 'never'.
```

### Type: `undefined` & `null`



```ts
let y: undefined = undefined;
let z: null = null;
console.log(typeof y); // undefined
console.log(typeof z); // null
y = 1; // Error: Type '1' is not assignable to type 'undefined'
```

## Arrays

รูปแบบทั่วไป ประกาศ type อะไร --> value ทั้งหมดต้องเป็น type นั้น

```ts
const names: string[] = [];
names.push("Dylan"); // no error
// names.push(3); // Error: Argument of type 'number' is not assignable to parameter of type 'string'.
```

### Readonly

ไม่สามารถเปลี่ยนค่าใน array ได้

```ts
const names: readonly string[] = ["Dylan"];
names.push("Jack"); // Error: Property 'push' does not exist on type 'readonly string[]'
```

### Type Inference

ts สามารถ infer type ของ array ได้ เหมือนตัวแปร

```ts
const numbers = [1, 2, 3]; // inferred to type number[]
numbers.push(4);
numbers.push("2"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
let head: number = numbers[0]; // no error
```

## Tuples

คือ array ที่ประกาศ type ของ values ได้มากกว่า 1 type โดยเรียงตาม index

```ts
// define our tuple
let ourTuple: [number, boolean, string];

// initialize correctly
ourTuple = [5, false, 'Coding God was here']; // correct
ourTuple = [false, 'Coding God was mistaken', 5]; // error
```

### Readonly Tuple

บางที เราอาจจะเพิ่ม value เกินกว่า type ทำให้ value นั้น ไม่มี type safety

```ts
// define our tuple
let ourTuple: [number, boolean, string];
// initialize correctly
ourTuple = [5, false, 'Coding God was here'];
// We have no type safety in our tuple for indexes 3+
ourTuple.push('Something new and wrong');
console.log(ourTuple); // [5, false, 'Coding God was here', 'Something new and wrong']
```

ถ้าไม่ให้เพิ่ม สามารถใช้ `readonly` เหมือน array ได้

```ts
// define our readonly tuple
const ourReadonlyTuple: readonly [number, boolean, string] = [5, true, 'The Real Coding God'];
// throws error as it is readonly.
ourReadonlyTuple.push('Coding God took a day off');
```

### Named Tuples

สามารถเพิ่ม context (ชื่อหรือคำอธิบาย) ให้กับ values ใน tuple ได้

```ts
const graph: [x: number, y: number] = [55.2, 41.3];
console.log(graph); // [ 55.2, 41.3 ]
console.log(graph[0]); // 55.2
console.log(graph[x]); // Cannot find name 'x'
console.log(x); // Cannot find name 'x'
```

### Destructuring Tuples

สามารถประกาศตัวแปรแบบ destructuring ได้

```ts
const graph: [number, number] = [55.2, 41.3];
const [x, y] = graph;
console.log(x); // 55.2
```

## Object Types

ประกาศตัวแปรแบบ object

```ts
const car: { type: string, model: string, year: number } = {
  type: "Toyota",
  model: "Corolla",
  year: 2009
};
console.log(car); // { type: 'Toyota', model: 'Corolla', year: 2009 }
```

### Type Inference

infer type ได้

```ts
const car = {
  type: "Toyota",
};
car.type = "Ford"; // no error
car.type = 2; // Error: Type 'number' is not assignable to type 'string'
```

### Optional Properties

การประกาศ type ของ property ข้างใน จำเป็นต้อง define ทุกอัน ไม่งั้น error

```ts
const car: { type: string, mileage: number } = {
  type: "Toyota",
};
car.mileage = 2000;
console.log(car);
// Error: Property 'mileage' is missing in type '{ type: string; }' but required in type '{ type: string; mileage: number; }'
```

ทางแก้ --> ใช้ (`?`) ข้างหลัง key กลายเป็น optional property

```ts
const car: { type: string, mileage?: number } = {
  type: "Toyota"
};
car.mileage = 2000;
console.log(car); // { type: 'Toyota', mileage: 2000 }
```

### Index Signatures

ใช้ประกาศ type โดยที่ไม่ต้องมี list ของ property

```ts
const nameAgeMap: { [index: string]: number } = {};
nameAgeMap.Jack = 25; // no error
nameAgeMap.Mark = "Fifty"; // Error: Type 'string' is not assignable to type 'number'.
```

Index Signatures สามารถใช้งานได้เหมือนกับ `Utility Types` (`Record<string, number>`)

## Enums

เป็น special "class" ที่ property เป็นค่าคงที่ ซึ่งมี 2 แบบคือ
- `string`
- `numeric`

### Numeric Enums

property จะเป็น number

#### Numeric Enums - Default

property จะมีค่าเริ่มจาก 0 และเพิ่มทีละ 1 ตามลำดับ

```ts
enum CardinalDirections {
  North,
  East,
  South,
  West
}
console.log(CardinalDirections.North); // 0
console.log(CardinalDirections.East); // 1
console.log(CardinalDirections.South); // 2
console.log(CardinalDirections.West); // 3
```

#### Numeric Enums - Initialized

คล้าย default แต่สามารถ set ค่าเริ่มต้นได้

```ts
enum CardinalDirections {
  North = 1,
  East,
  South,
  West
}
console.log(CardinalDirections.North); // 1
console.log(CardinalDirections.West); // 4
```

#### Numeric Enums - Fully Initialized

set ค่าให้ทั้งหมด

```ts
enum StatusCodes {
  NotFound = 404,
  Success = 200,
  Accepted = 202,
  BadRequest = 400
}
console.log(StatusCodes.NotFound); // 404
```

### String Enums

enum ที่เป็น string

```ts
enum CardinalDirections {
  North = 'North',
  East = "East",
  South = "South",
  West = "West"
};
console.log(CardinalDirections.North); // North
```

จริงๆแล้ว สามารถผสม string กับ numeric ใน enum เดียวกันได้ แต่ไม่แนะนำ

## Type Aliases and Interfaces

ใน ts เราสามารถประกาศ type แยกจากตัวแปรได้ แล้วค่อยเอามาใช้ทีหลัง มี 2 แบบคือ

### Type Aliases

ประกาศ type แบบให้ชื่อใหม่

```ts
type Point = {
  x: number;
  y: number;
};
const point: Point = {
  x: 5,
  y: 10
};
console.log(point); // { x: 5, y: 10 }
```

### Interfaces

ประกาศ type แบบให้ชื่อใหม่ และสามารถ extend ได้ ใช้สำหรับ object เท่านั้น

```ts
interface Point {
  x: number;
  y: number;
}
const point: Point = {
  x: 5,
  y: 10
};
console.log(point); // { x: 5, y: 10 }
```

ตัวอย่างการ extend

```ts
interface Point {
  x: number;
  y: number;
}
interface Point3D extends Point {
  z: number;
}
const point3D: Point3D = {
  x: 5,
  y: 10,
  z: 20
};
console.log(point3D); // { x: 5, y: 10, z: 20 }
```

หรือใช้ `extends` กับ type alias ได้เหมือนกัน

```ts
type Point = {
  x: number;
  y: number;
};
type Point3D = Point & { z: number };
const point3D: Point3D = {
  x: 5,
  y: 10,
  z: 20
};
console.log(point3D); // { x: 5, y: 10, z: 20 }
```

## Union Types

ประกาศ type ที่เป็นไปได้หลาย type

```ts
let union: string | number;
union = "string";
union = 3;
union = true; // Error: Type 'boolean' is not assignable to type 'string | number'.
```

สามารถใช้กับ object ได้

```ts
let union: { name: string } | { age: number };
union = { name: "Dylan" };
union = { age: 25 };
union = { name: "Dylan", age: 25 }; // Error: Object literal may only specify known properties, and 'age' does not exist in type '{ name: string; } | { age: number; }'.
```

```ts
function printStatusCode(code: string | number) {
  console.log(`My status code is ${code}.`)
}
printStatusCode(404);
printStatusCode('404');
printStatusCode(true); // Error: Argument of type 'boolean' is not assignable to parameter of type 'string | number'.
```

### Union Type Errors

เมื่อใช้ union type แล้ว ถ้าเราใช้ method หรือ property ที่ไม่ได้อยู่ในทั้ง 2 type ที่เป็นไปได้ ก็จะ error

```ts
let union: string | number;
union = "string";
union = 3;
console.log(union.length); // Error: Property 'length' does not exist on type 'string | number'.
```

## Functions

ประกาศ type ของ function

### Return Type

ประกาศ return type ของ function

```ts
function add(a: number, b: number): number {
  return a + b;
}
console.log(add(5, 10)); // 15
```

ถ้าไม่ประกาศ return type จะ infer ได้

```ts
function add(a: number, b: number) {
  return a + b;
}
console.log(add(5, 10)); // 15
```

### Void Return Type

ถ้าไม่มีการ return ค่าอะไร ให้ประกาศ return type เป็น `void`

```ts
function log(message: string): void {
  console.log(message);
}
log("Hello, world!"); // Hello, world!
```

### Parameters

ประกาศ type ของ parameter ได้

```ts
function log(message: string, userId?: string) {
  console.log(message, userId);
}
log("Hello, world!"); // Hello, world! undefined
log("Hello, world!", "Dylan"); // Hello, world! Dylan
```

ถ้าไม่มีการประกาศ type ของ parameter จะเป็น `any` จนกว่าจะใส่ type ให้

#### Optional Parameters

ประกาศ parameter ให้เป็น optional ได้

```ts
function log(message: string, userId?: string) {
  console.log(message, userId);
}
log("Hello, world!"); // Hello, world! undefined
log("Hello, world!", "Dylan"); // Hello, world! Dylan
```

#### Default Parameters

ประกาศ parameter ให้มีค่าเริ่มต้นได้

```ts
function log(message: string, userId = "Not signed in") {
  console.log(message, userId);
}
log("Hello, world!"); // Hello, world! Not signed in
log("Hello, world!", "Dylan"); // Hello, world! Dylan
```

#### Named Parameters

ประกาศ parameter ให้มีชื่อได้ โดยใช้ object

```ts
function divide({ dividend, divisor }: { dividend: number, divisor: number }) {
  return dividend / divisor;
}
console.log(divide({dividend: 10, divisor: 2})); // 5
```

#### Rest Parameters

ประกาศ parameter ให้เป็น rest ได้

```ts
function add(a: number, b: number, ...rest: number[]) {
  return a + b + rest.reduce((p, c) => p + c, 0);
}
console.log(add(10,10,10,10,10)); // 50
```

### Type Alias

ประกาศ type ของ function แล้วเอามาใช้

```ts
type Add = (a: number, b: number) => number;
const add: Add = (a, b) => a + b;
console.log(add(5, 10)); // 15
```

## Casting

การเปลี่ยน type ของตัวแปร โดยใช้ `as` keyword หรือ `<>` ในการ cast <br>
บางครั้งในการทำงาน เราจำเป็นต้องเปลี่ยน type ของตัวแปร เช่นการรับค่าที่ type ไม่ถูกต้อง มาจาก Library

### Casting with `as`

การใช้ `as` keyword ในการ cast

```ts
let value: any = "Hello, world!";
let length: number = (value as string).length;
console.log(length); // 13

let x: unknown = 'hello';
console.log((x as string).length); // 5
console.log(typeof x); // string
```

เราไม่สามารถ cast ได้เสมอไป ถ้าไม่เป็น type ที่ถูกต้อง ก็จะ error

```ts
let x: unknown = 4;
console.log((x as string).length); // prints undefined since numbers don't have a length
console.log(typeof x); // number
```

### Casting with `<>`

การใช้ `<>` ในการ cast

```ts
let value: any = "Hello, world!";
let length: number = (<string>value).length;
console.log(length); // 13

let x: unknown = 'hello';
console.log((<string>x).length); // 5
console.log(typeof x); // string
```

การ cast แบบนี้ไม่สามารถทำงาน บน `TSX` ได้

### Force casting

ตัองการ override type ของตัวแปร ที่ error โดยเปลี่ยนเป็น `unknown` ก่อน แล้วค่อย cast กลับเป็น type ที่ต้องการ

```ts
let x = 'hello';
console.log(((x as unknown) as number).length); // x is not actually a number so this will return undefined
```
