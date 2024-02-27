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
