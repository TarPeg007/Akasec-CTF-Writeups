# Trenbolone Acetate Crypto Challenge Write-Up

This repository contains a detailed solution to the **"Trenbolone Acetate"** crypto challenge from the Akasec CTF. The challenge tests your understanding of RSA encryption, key generation, and vulnerabilities related to the manipulation of prime numbers.

---

## Challenge Description

**Name:** Trenbolone Acetate  
**Difficulty:** Medium  
**Category:** Cryptography  
**Description:**  
The challenge presents a flawed implementation of RSA encryption where the modulus \( N \) is updated incrementally by adding new primes during each encryption step. Our goal is to exploit the weakness in the implementation to recover the flag.
![Homepage Screenshot](https://github.com/TarPeg007/Akasec-CTF-Writeups/blob/master/photos/Screen%20Shot%202024-11-26%20at%201.27.29%20PM.png?raw=true)
---

## Challenge Script

The server script implements the following functionality:
1. **Prime Generation:** New primes are generated for each encryption step, and these are multiplied to extend the modulus \( N \).
2. **Encryption:** A fixed flag is encrypted using RSA with \( e = 0x10001 \).
3. **Predict \( N \):** If you can predict the current \( N \), the server provides the flag.

---

## Observations

1. **Initial Prime Assignment:**  
   The modulus \( N \) begins as the product of two primes, \( p \) and \( q \), generated using `getPrime(1024)`.

2. **Vulnerability in \( N \):**  
   During each round, new primes are added, and the modulus \( N \) is recalculated. However, in the challenge, only the last prime \( p \) is revealed, making it possible to infer \( N \) if only one prime is used (as in the provided solution script).

3. **Simplified RSA Structure:**  
   For cases where \( N = p \), \( \phi(N) = p - 1 \). This makes decryption straightforward with the knowledge of \( p \) and \( e \).

---

## Solution

Here’s the step-by-step solution:

### 1. Extract Known Values
From the server output:
- \( p \) is provided.
- The encrypted flag \( c \) is given.

### 2. Infer \( N \)
As \( N \) is \( p \), the modulus \( N \) and the totient \( \phi(N) \) are easy to compute:
\[
\phi(N) = p - 1
\]

### 3. Decrypt the Flag
Using the formula for RSA decryption:
\[
d = e^{-1} \mod \phi(N)
\]
\[
m = c^d \mod N
\]
where \( m \) is the plaintext flag.

### 4. Recover the Flag
Convert the plaintext \( m \) back to bytes to reveal the flag.

---

## Script

Here’s the Python script used to solve the challenge:

```python
from Crypto.Util.number import *

# Given values
encrypted_flag = 51483095552140653364864611178605362696356849162380759050753875449168357321739520258869848469102904821311705317056787456024128377266332611289816462442225400186960166018722570795863971255999000189895972481251170590124549508072187569420212134158966594457907646099456700920249658595300282708704126429761856013429838295177464263627828648139302778687870326862325260568151071809326620898400377077076461141410522221303298135576029373979884485178520411678164351672077364871920434095729776689024144637519661160067638383823713407065326053432174361415628263000311594397503766312745295697638665999615343861510508748257613692105380983244826612580794581984051323397293327345068846466966124594130432166160146360941812918517889885612851756396987556472751685718556738033116281074222496654682692480917359791745441884759114699351933659923967625947690440565535967055452135953987666857434297498316569067549248016750033053191869578674525382288900418866462795610006147666320919628289324242722024405263996612525346838744312332798369405017647025402798946145756535937768132708946067646836026955125373006239104517710598449522076251300545536091365090935661556161530790279749752135049629868008363993805574878525558025123296749531429851721314381257441993012848257
p = 136481980716408391567374244447384154235262095602507568456167340760839375359405997568832781813140858869520139150045243471250205760875700605462782687154864746016594582907078782991578335385985808773413539886523296066942357172090711655578679508853286509079006639647868682236271706191985859846022904872579882285309

# Compute N and φ(N)
n = p
phi = p - 1

# RSA parameters
e = 0x10001
d = pow(e, -1, phi)

# Decrypt the flag
m = pow(encrypted_flag, d, n)
flag = long_to_bytes(m)

print(f"The flag is: {flag.decode()}")

```plaintext
ODYSSEY{aywa_aywa_ana_m3ak}
```
