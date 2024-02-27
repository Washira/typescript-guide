# TypeScript Guide

- [TypeScript Guide](#typescript-guide)
  - [Simple Types](#simple-types)
    - [Type Assignment](#type-assignment)
      - [Explicit Type](#explicit-type)
      - [Implicit Type](#implicit-type)


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

```ts
let firstName = "Dylan";
```

