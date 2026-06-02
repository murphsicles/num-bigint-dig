# @math/num-bigint-dig v0.1.0

**Big integer arithmetic for Zeta** — ported from Rust's `num-bigint-dig` crate.

Provides both signed (`BigInt`) and unsigned (`BigUint`) arbitrary-precision integer types with a comprehensive set of arithmetic, comparison, bitwise, modular, and cryptographic operations.

Designed for use in the **Zorbs GG20 threshold ECDSA** implementation and other cryptographic applications including **Paillier cryptosystem** support.

---

## Installation

```json
{
  "dependencies": {
    "@math/num-bigint-dig": "0.1.0"
  }
}
```

---

## Features

### Core Types

| Type | Description |
|------|-------------|
| `BigUint` | Unsigned arbitrary-precision integer (little-endian `u64` limbs) |
| `BigInt` | Signed arbitrary-precision integer (sign + magnitude) |
| `Sign` | Enum with `Positive`, `Negative`, `Zero` |

### Operations

- **Creation**: `from_u64()`, `from_u128()`, `from_bytes_be()`, `from_bytes_le()`, `from_str_radix()`
- **Arithmetic**: `add()`, `sub()`, `mul()`, `div()`, `rem()`, `div_rem()`, `pow()`, `neg()`
- **Comparison**: `eq()`, `ne()`, `lt()`, `gt()`, `le()`, `ge()`, `cmp()`
- **Bitwise**: `and()`, `or()`, `xor()`, `shl()`, `shr()`
- **Modular**: `modpow()`, `mod_inverse()`, `mod_n_add()`, `mod_n_mul()`, `mod_n_square()`
- **Number Theory**: `gcd()`, `lcm()`, `is_power_of_two()`, `next_power_of_two()`
- **Properties**: `is_zero()`, `is_one()`, `is_even()`, `is_odd()`, `bit_length()`, `trailing_zeros()`
- **Primality**: `is_prime()` (Miller-Rabin probabilistic), `is_prime_u64()` (deterministic for < 2^64)
- **Random**: `random(bit_length)`, `random_range()`, `gen_random_bits()`
- **Conversion**: `to_bytes_be()`, `to_bytes_le()`, `to_str_radix()`, `to_u64()`, `to_u128()`

### Paillier-Specific Features

- `gen_safe_prime(bits)` — generates safe prime `p = 2q + 1` (both prime)
- `gen_paillier_modulus(bits)` — generates `n = p * q` for Paillier key generation
- `mod_n_add()`, `mod_n_mul()`, `mod_n_square()` — modular arithmetic in Z_n
- `mod_n_pow()` — modular exponentiation

### num-integer Integration

- `gcd()`, `lcm()`
- `is_even()`, `is_odd()`
- `next_power_of_two()`
- `div_floor()`, `div_ceil()`
- `is_multiple_of()`, `divides()`

---

## Usage Examples

### Creating BigUints

```zeta
import { BigUint } from "@math/num-bigint-dig";

// From unsigned integers
let a = BigUint::from_u64(42);
let b = BigUint::zero();
let c = BigUint::one();

// From bytes (big-endian)
let bytes: [u8; 4] = [0xDE, 0xAD, 0xBE, 0xEF];
let d = BigUint::from_bytes_be(bytes);

// From hex string
let e = BigUint::from_str_radix("deadbeef", 16).unwrap();
```

### Arithmetic

```zeta
let x = BigUint::from_u64(100);
let y = BigUint::from_u64(37);

let sum = x.add(&y);        // 137
let diff = x.sub(&y);       // 63
let prod = x.mul(&y);       // 3700
let quot = x.div(&y);       // 2
let rem = x.rem(&y);        // 26
let (q, r) = x.div_rem(&y); // (2, 26)
```

### Signed Arithmetic

```zeta
let a = BigInt::from_i64(10);
let b = BigInt::from_i64(-3);

let sum = a.add(&b);    // 7
let diff = a.sub(&b);   // 13
let prod = a.mul(&b);   // -30
```

### Modular Arithmetic

```zeta
// Modular exponentiation
let base = BigUint::from_u64(7);
let exp = BigUint::from_u64(4);
let modulus = BigUint::from_u64(13);
let result = base.modpow(&exp, &modulus); // 7^4 mod 13 = 9

// Modular inverse
let a = BigUint::from_u64(3);
let m = BigUint::from_u64(11);
let inv = a.mod_inverse(&m).unwrap(); // 4 (3*4 = 12 = 1 mod 11)

// Paillier modular arithmetic
let n = BigUint::from_u64(100);
let x = BigUint::from_u64(37);
let y = BigUint::from_u64(48);
let s = BigUint::mod_n_add(&x, &y, &n); // 85
let sq = BigUint::mod_n_square(&x, &n); // 37^2 mod 100 = 69
```

### Number Theory

```zeta
let a = BigUint::from_u64(12);
let b = BigUint::from_u64(18);

let g = a.gcd(&b);  // 6
let l = a.lcm(&b); // 36

// Power of two
let n = BigUint::from_u64(5);
let p2 = n.next_power_of_two(); // 8
```

### Primality Testing

```zeta
// Deterministic for 64-bit
assert(BigUint::is_prime_u64(17));  // true

// Probabilistic for larger values
let large = BigUint::from_str_radix("10000000000000000000000000000000000000000000000001", 10).unwrap();
let prime = large.is_prime();
```

### Random Generation

```zeta
// Random 256-bit number with MSB set
let r = BigUint::random(256);

// Random number in range [min, max]
let min = BigUint::from_u64(1000);
let max = BigUint::from_u64(9999);
let range = BigUint::random_range(&min, &max);
```

### Safe Prime Generation (Paillier)

```zeta
// Generate a 512-bit safe prime p = 2q + 1
let safe_prime = BigUint::gen_safe_prime(512);

// Generate Paillier modulus n = p * q
let (n, p, q) = BigUint::gen_paillier_modulus(2048);
// n is the Paillier modulus, p and q are the safe prime factors
```

### Byte Conversion

```zeta
let n = BigUint::from_u64(0xDEADBEEF);
let be_bytes = n.to_bytes_be(); // [0xDE, 0xAD, 0xBE, 0xEF]
let le_bytes = n.to_bytes_le(); // [0xEF, 0xBE, 0xAD, 0xDE]
```

### String Conversion

```zeta
let n = BigUint::from_u64(255);
assert(n.to_str_radix(16) == "ff");

let decoded = BigUint::from_str_radix("ff", 16).unwrap();
assert(decoded.eq(&n));

// BigInt with sign
let neg = BigInt::from_i64(-255);
assert(neg.to_str_radix(16) == "-ff");
```

---

## Internal Representation

Both `BigUint` and `BigInt` use a **little-endian** array of `u64` limbs:

- `BigUint { data: Vec<u64> }` — `data[0]` is the least significant limb
- `BigInt { sign: Sign, data: Vec<u64> }` — `data` stores the magnitude only

The most significant limb is never zero (except for the value zero itself).

---

## Security Considerations

- **Miller-Rabin primality tests are probabilistic** — for cryptographic use, the number of rounds provides adequate confidence (16 rounds for < 256-bit, 8 for < 1024-bit, 4 for < 2048-bit, 3 for ≥ 2048-bit)
- **Random generation uses `@crypto/rand`** for cryptographically secure random bytes
- **Safe prime generation** may be slow for large bit sizes; use appropriate bit lengths for your security requirements
- **Constant-time operations** are not guaranteed — this library is designed for correctness, not side-channel resistance

---

## Comparison with Rust num-bigint-dig

This Zeta port preserves the API patterns and algorithm choices of the Rust `num-bigint-dig` crate while adapting to Zeta's syntax and language features:

- No lifetimes or borrowing semantics (Zeta uses owned values)
- `pub fn` for public, `fn` for private
- Error handling via `Option<T>` where applicable
- All functionality in a single `mod.z` module

---

## API Reference

### BigUint

```zeta
pub struct BigUint {
    data: Vec<u64>,
}
```

| Method | Returns | Description |
|--------|---------|-------------|
| `zero()` | `BigUint` | Creates zero |
| `one()` | `BigUint` | Creates one |
| `from_u64(val)` | `BigUint` | Creates from u64 |
| `from_u128(val)` | `BigUint` | Creates from u128 |
| `from_bytes_be(bytes)` | `BigUint` | Creates from big-endian bytes |
| `from_bytes_le(bytes)` | `BigUint` | Creates from little-endian bytes |
| `from_str_radix(s, radix)` | `Option<BigUint>` | Parses from string |
| `is_zero()` | `bool` | Checks if zero |
| `is_one()` | `bool` | Checks if one |
| `is_even()` | `bool` | Checks if even |
| `is_odd()` | `bool` | Checks if odd |
| `bit_length()` | `u64` | Number of bits |
| `trailing_zeros()` | `u64` | Number of trailing zero bits |
| `add(other)` | `BigUint` | Addition |
| `sub(other)` | `BigUint` | Subtraction |
| `mul(other)` | `BigUint` | Multiplication |
| `div(other)` | `BigUint` | Division |
| `rem(other)` | `BigUint` | Remainder |
| `div_rem(other)` | `(BigUint, BigUint)` | Quotient and remainder |
| `pow(exp)` | `BigUint` | Power |
| `modpow(exp, mod)` | `BigUint` | Modular exponentiation |
| `mod_inverse(mod)` | `Option<BigUint>` | Modular inverse |
| `gcd(other)` | `BigUint` | Greatest common divisor |
| `lcm(other)` | `BigUint` | Least common multiple |
| `and(other)` | `BigUint` | Bitwise AND |
| `or(other)` | `BigUint` | Bitwise OR |
| `xor(other)` | `BigUint` | Bitwise XOR |
| `shl(shift)` | `BigUint` | Left shift |
| `shr(shift)` | `BigUint` | Right shift |
| `eq/ne/lt/gt/le/ge/cmp` | `bool/i32` | Comparisons |
| `is_prime()` | `bool` | Miller-Rabin primality test |
| `next_power_of_two()` | `BigUint` | Next power of two |
| `to_bytes_be()` | `Vec<u8>` | Big-endian bytes |
| `to_bytes_le()` | `Vec<u8>` | Little-endian bytes |
| `to_str_radix(radix)` | `String` | String in given radix |
| `to_u64()` | `u64` | Fits to u64 (low word) |
| `to_u128()` | `u128` | Fits to u128 |
| `random(bits)` | `BigUint` | Random of given bit length |
| `random_range(min, max)` | `BigUint` | Random in range |
| `gen_safe_prime(bits)` | `BigUint` | Safe prime generation |
| `gen_paillier_modulus(bits)` | `(BigUint, BigUint, BigUint)` | Paillier modulus |

### BigInt

```zeta
pub struct BigInt {
    sign: Sign,
    data: Vec<u64>,
}
```

| Method | Returns | Description |
|--------|---------|-------------|
| `zero()` | `BigInt` | Creates zero |
| `one()` | `BigInt` | Creates one |
| `from_u64(val)` | `BigInt` | Creates from u64 |
| `from_i64(val)` | `BigInt` | Creates from i64 |
| `from_biguint(sign, val)` | `BigInt` | Wraps BigUint with sign |
| `from_str_radix(s, radix)` | `Option<BigInt>` | Parses from string |
| `is_zero/is_positive/is_negative` | `bool` | Sign checks |
| `abs()` | `BigUint` | Absolute value |
| `add/sub/mul/div/rem/pow` | `BigInt` | Arithmetic |
| `neg()` | `BigInt` | Negation |
| `eq/ne/lt/gt/le/ge/cmp` | `bool/i32` | Comparisons |
| `gcd/lcm` | `BigInt` | Number theory |
| `and/or/xor/shl/shr` | `BigInt` | Bitwise |
| `to_str_radix` | `String` | String conversion |

---

## License

MIT — see LICENSE

---

## Related

- [Rust num-bigint-dig](https://crates.io/crates/num-bigint-dig) — original implementation
- [Zorbs GG20 Threshold ECDSA](https://github.com/murphsicles/zorbs) — primary consumer of this library
- [@math/num-bigint](https://github.com/murphsicles/zeta-num-bigint) — alternative big integer library
