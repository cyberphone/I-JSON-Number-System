# Extended Numeric Data Types Compliant with I-JSON

*I-JSON [[RFC7493](https://tools.ietf.org/html/rfc7493)] defines rules which enable
interoperability between between different implementations. Although useful, 
many applications depend on extended numeric data which currently is dealt with in 
non-standard ways. This document describes some of the more common variants,
with the goal of eventually establishing some kind of standard, be it de-facto or real.*

## The Problem in a Nutshell
The following JSON object highlights some of the issues with JSON numbers:
```json
{
  "giantNumber": 1.4e+9999,
  "payMeThis": 26000.33,
  "int64Max": 9223372036854775807
}
```
- `giantNumber` does not generally parse but could still be usable in certain contexts
- `payMeThis` is probably not meant to be processed by a system based on traditional floating point due to potential *rounding errors*
- `int64Max` parses in all JSON systems but *loses precision* using ECMAScript's `JSON.parse()`

## Core Extension Mechanism
Since I-JSON numbers are constrained by IEEE-754 double precision, extended number data
types **must** be enclosed within double quotes, i.e. have JSON "string" syntax,
permitting basic parsing and serialization of extended data types by *virtually any* JSON tool:
```json
{
  "giantNumber": "1.4e+9999"
}
```

## Recognizing and Processing Extended Numeric Data Types
A remaining issue is how a JSON parser can know how to recognize and process
extended numeric data types.  Fortunately, there are multiple solutions for that.
Below is a programmatic variant (here expressed in JavaScript) which is useful for processing
*dynamic* JSON:
```javascript
var obj = JSON.parse('{"giantNumber": "1.4e+9999"}');
var biggie = new BigNumber(obj.giantNumber);
```
In a *declarative* system, data types are usually resolved automatically like in this Java sample:
```java
class MyObject {
    String type;
    BigInteger serialNumber;
 }
 ```
    
## Extended Numeric Data Types
Table TBD...
