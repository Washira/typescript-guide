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
```

