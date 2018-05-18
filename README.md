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
Since I-JSON numbers are constrained by IEEE-754 double precision, extended numeric data
types **must** in this specification be enclosed within double quotes, i.e. have JSON "string" syntax,
permitting basic parsing and serialization of extended data types by *virtually any* JSON tool:
```json
{
  "giantNumber": "1.4e+9999"
}
```

## Recognizing and Processing Extended Numeric Data Types
A remaining issue is how a JSON parser can know how to recognize and process
extended numeric data types.  Fortunately, there are multiple solutions for that.
Below is a *programmatic* variant (here expressed in JavaScript). relying on
name conventions between sender and receiver:
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
<table>
  <tr><th>Type</th><th>Description</th><th>Range</th><th>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JSON&nbsp;Syntax&nbsp;*&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</th></tr>
  <tr><td><b>Long</b></td><td>Signed 64-bit integer</td><td>-9223372036854775808 to 9223372036854775807</td><td>0|-?[1-9][0-9]*</td></tr>
  <tr><td><b>Ulong</b></td><td>Unsigned 64-bit integer</td><td>0 to 18446744073709551615</td><td>0|[1-9][0-9]*</td></tr>
  <tr><td><b>BigInteger</b></td><td>Arbitrary large signed integer</td><td>-<i>Arbitrary</i> to <i>Arbitrary</i></td><td>0|-?[1-9][0-9]*</td></tr>
  <tr><td><b>BigDecimal</b></td><td>Arbitrary large decimal number</td><td>-<i>Arbitrary</i> to <i>Arbitrary</td><td>-?[0-9]+(\.[0-9]+)?(e(-|\+)[0-9]+)?</td></tr>
  <tr><td><b>Money</b></td><td>Decimal number expressing money</td><td>-<i>Sufficient</i> to<i> Sufficient</i></td><td>-?[1-9][0-9]*\.[0-9]+</td></tr>
</table>

\* Minus the double quotes.

*Sufficient* denotes earthly monetary sums.

## Existing Solutions
A few existing solutions have been investigated.

### Java JSON-P
JSON-P currently uses *Adaptive Notation* where the actual value determines if it is to be serialized as a JSON Number or be embedded in a JSON String:
```json
{
  "BigDecimal.large":"1E+999",
  "BigDecimal.small":1,
  "BigInteger.large":"1787787787877878787878787878787",
  "BigInteger.small":1,
  "Long.large":9223372036854775807,
  "Long.small":1
}
```
This scheme is with the exception of "Long.large" compatible with `JSON.parse()`;

### Json.NET
Json.NET doesn't seem to have any global option governing number serialization.
The defaults are:
```
{
  "BigInteger.large": 878787878787878787878787878787,
  "BigInteger.small": 1,
  "Decimal.large": 3777777447789999999445.678997,
  "Decimal.small": 1.0,
  "Long.large": 9223372036854775807,
  "Long.small": 1
}
```
This is not compliant with `JSON.parse()`;

### Twitter
The Tweet Object shows the downside of not having a unified way of dealing with big numbers:
```json
{
  "created_at": "Thu Apr 06 15:24:15 +0000 2017",
  "id": 850006245121695744,
  "id_str": "850006245121695744",
  "text": "This is what I'm saying :-)",
  "user": {},  
  "entities": {}
}
```


V0.12
