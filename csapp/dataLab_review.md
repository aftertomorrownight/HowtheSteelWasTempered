
# DataLab Review Session Notes

## Integer Problems

### 1. bitXor - x^y using only ~ and &
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1


```python
int bitXor(int x, int y) {
  int a = ~x & y;
  int b = ~y & x;
  // return a | b
  return ~(~a & ~b);

```

 ### 2. tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1


```python
int tmin(void) {
  return 1 << 31;
}
```

 ### 3. isTmax - returns 1 if x is the maximum, two's complement number, and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1

* tmax and -1 are only 2 integers that all bits will change when add 1

tmax = 01111...111 

tmax + 1 = 10000...000


-1 = 11111...111 

-1 + 1 = 00000...000

* exclude -1 from the result


Tmax or -1 = !(~(x+1))^x = a

-1 or else = !x^(~0) = b

ab 1 0

1  0 1(Tmax)

0  x 0


```python
int isTmax(int x) {
  return (!(~(x+1))^x) ^ (!x^(~0));
}
```

 ### 4. allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2


```python
// shift x
int allOddBits(int x) {
  int mask = 0xAA; //00000000...0010101010
  return !((x&mask & ((x>>8)&mask) & ((x>>16)&mask) & ((x>>24)&mask)) ^ mask);
}

// shift mask
int allOddBits(int x) {
  int b_10101010 = 170; // aa
  int x_aaaaaaaa = b_10101010 + (b_10101010 << 8) + (b_10101010 << 16) + (b_10101010 << 24);
  int res = (x_aaaaaaaa & x) ^ x_aaaaaaaa; 
  return !res;
}
```

 ### 5. negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2


```python
int negate(int x) {
  return ~x + 1;
}
```

### 6. isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3


```python
// straightforward number comparing way
int isAsciiDigit(int x) {
  int greaterThan0 = !(x >> 31);
  int largerThanLeft = !((x + (~0x30 + 1)) >> 31);
  int lessThanRight =  !((0x39 + (~x + 1)) >> 31);
  return greaterThan0 & largerThanLeft & lessThanRight;
}

// bits compare
// 1 1 0 0 0 0
// 1 1 0 0 0 1
// ...
// 1 1 0 1 1 1
// 1 1 1 0 0 0
// 1 1 1 0 0 1
// 看最后六位
// pre_req: 前面必须都是 000...11****
// condition_one: 如果是 110：那肯定是对的 110***
// condition_two: 如果是 111：那么只能是 11100*

int isAsciiDigit(int x) {
  int fifth_sixth_is_11 = (x >> 4) ^ 3; 
  int condition_one = (x & 8); // 1*** is 8 else 0
  int condition_two = (x >> 1) ^ 28; // last six is 11100*

  int pre_req = !fifth_sixth_is_11;
  int a = pre_req & !condition_one;
  int b = pre_req & !condition_two;
  return a | b;
}
```

### 7. conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3


```python
int conditional(int x, int y, int z) {
  int mask = !x + (~0);
  return (mask & y) + (~mask & z);
}
```

 ### 8. isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3


```python
int isLessOrEqual(int x, int y) {
  int xLessThan0 = x >> 31;
  int yLargerOrEqualThan0 = !(y >> 31);
  int sameSign = !((x >> 31) ^ (y >> 31));

  return ((!sameSign) & xLessThan0 & yLargerOrEqualThan0) | (sameSign & !((y + (~x + 1)) >> 31));
}
```

 ### 9. logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 


```python
// & each bits
int logicalNeg(int x) {
   int x16 = x | (x >> 16);
   int x8 = x16 | (x16 >> 8);
   int x4 = x8 | (x8 >> 4);
   int x2 = x4 | (x4 >> 2);
   int x1 = x2 | (x2 >> 1);
   return (~x1) & 0x1;
}

// only 0 and Tmin have ~x + 1 == x
// ~x + 1 == x
// ~Tmin + 1 == Tmin

// 0 or Tmin : x ^ -x = 0........
// else: x ^ -x = 1........

int logicalNeg(int x) {
  int negtive = (~x) + 1;
  int x_xor_negtive = negtive ^ x; // Except 0 and Tmin, all numbers should start with 1
  int zero_or_minus1 = x_xor_negtive >> 31;
  return (~(x >> 31)) & (zero_or_minus1 + 1);
}

// 0 is the only integer that 0's MSB is 0 and (0-1)'s MSB is 1
int logicalNeg(int x) {
  int a = (x >> 31) + 1; // should be 1
  int b = ((x + (~1 + 1)) >> 31) + 1; // should be 0
  return a & (~b + 1 + 1);
}
```

### 10. howManyBits - return the minimum number of bits required to represent x in two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4


```python
// binary search to count the redundant sign bits in the front
int howManyBits(int x) {
  int ans = 0;
  int temp = (x >> 31) ^ x;

  int left = temp >> 16;
  int right = temp & (~(~0 << 16));
  int mask = !left + (~0);
  temp = (mask & left) + (~mask & right);
  ans = ans + ((~mask) & 16);

  left = temp >> 8;
  right = temp & (~(~0 << 8));
  mask = !left + (~0);
  temp = (mask & left) + (~mask & right);
  ans = ans + ((~mask) & 8);

  left = temp >> 4;
  right = temp & (~(~0 << 4));
  mask = !left + (~0);
  temp = (mask & left) + (~mask & right);
  ans = ans + ((~mask) & 4);

  left = temp >> 2;
  right = temp & (~(~0 << 2));
  mask = !left + (~0);
  temp = (mask & left) + (~mask & right);
  ans = ans + ((~mask) & 2);

  left = temp >> 1;
  right = temp & (~(~0 << 1));
  mask = !left + (~0);
  temp = (mask & left) + (~mask & right);
  ans = ans + ((~mask) & 1) + ((~mask) & (!right));

  ans = 33 + (~ans + 1);
  return ans;
}
```

## Float Point Problems

 ### 11. floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4


```python
unsigned floatScale2(unsigned uf) {
  int exp = uf & 0x7f800000; 
  int frac = uf & 0x007fffff;

//      exp = 255                  exp = 254
  if ((!(exp ^ 0x7f800000)) || (!(exp ^ 0x7f000000))) 
    return (uf & (~0x7f800000)) | (0x7f800000);

  if (!(exp ^ 0))
    return uf + frac; 
  
  return (uf + 0x00800000);
}
```

 ### 12. floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4


```python
int floatFloat2Int(unsigned uf) {
  int exp = uf & 0x7f800000;
  int frac = uf & 0x007fffff;
  int sign = uf & 0x80000000;
  int E = (exp >> 23) - 127;
  int ans = 0;

  if (E >= 31)
    return 0x80000000u;

  if (E < 0) {
    return 0;
  }

  if (E >= 23) {
    ans = (frac << (E - 23)) | (1 << E);
  } else {
    ans = (frac >> (23 - E)) | (1 << E);
  }
  
  if (sign) {
    return ~ans + 1;
  } else {
    return ans;
  }
}
```

### 13. floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4


```python
unsigned floatPower2(int x) {
    int exp = x + 127;

    if (x < -127) return 0;
    if (x > 127) return 0x7f800000;

    return exp << 23;
}
```
