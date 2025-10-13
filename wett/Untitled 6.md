This study guide summarizes the key concepts and primitives covered in the Foundations of Cryptography (CSC/MA 414) lectures so far, followed by detailed examples demonstrating how to construct proofs using common cryptographic techniques.

---

## I. Study Guide for CSC/MA 414: Foundations of Cryptography

The course focuses on defining security properties mathematically and proving schemes secure mathematically. The standard cryptographic approach involves three steps: Formal Definition, Assumptions, and Scheme and Proof (by reduction).

### A. Core Concepts and Background

1. **Required Background:** Students should be familiar with modular arithmetic, groups, basic probability, boolean logic, and logic proofs, including proof by contradiction.
2. **Cryptographic Approach:** When analyzing a scheme, we derive the **formal security definition** (defining what "secure" means and the power of the adversary), derive a **scheme** that meets the definition, and derive a **formal proof of security** that the scheme achieves the definition.
3. **Kerckhoff’s Principle:** The security of a scheme must not depend on the algorithms being secret; the adversary always knows everything except the private randomness/state (the key).
4. **Hard Problems (Assumptions):** Security proofs often rely on mathematical assumptions that "something is hard computationally for the adversary". Examples include:
    - **Discrete Logarithm Problem (DLP):** Given a cyclic group $G$ with generator $g$, and $Y = g^x$, find $x$.
    - **Decisional Diffie-Hellman (DDH) Assumption:** It is computationally hard to distinguish $(g^a, g^b, g^{ab})$ from $(g^a, g^b, g^c)$ where $a, b, c$ are random exponents.
    - **Factoring Problem:** Given $N=pq$, find factors $p$ and $q$ (where $p, q$ are large random primes of the same size).

### B. Unit 1: Perfect Security (Information-Theoretic Security)

1. **Formal Definition of Perfect Security:** An encryption scheme (Gen, Enc, Dec) is perfectly secure if, for every message $m \in M$ and every ciphertext $c \in C$, the a priori probability that $m$ is encrypted is the same as the a posteriori probability after observing the ciphertext $c$.
    - Mathematically: $\text{Pr}[M=m | C=c] = \text{Pr}[M=m]$.
    - Equivalently: Given a ciphertext $c$ and two arbitrary possible messages $m, m'$, they have the same probability of being the message encrypted: $\text{Pr}[M=m | C=c] = \text{Pr}[M=m' | C=c]$.
2. **Shift Cipher:** The shift cipher is **not** perfectly secure, which can be shown by counterexample (e.g., demonstrating that a ciphertext rules out certain plaintexts).
3. **One-Time Pad (OTP):** Defined by $C = K \oplus M$, where the key $K$ is chosen uniformly at random and is the same length as the message $M$.
    - **Proof of Security:** OTP is perfectly secure (proven using a direct proof/counting argument).
    - **Limitation:** To achieve perfect security, the key space size must be greater than or equal to the message space size ($|K| \ge |M|$). A fresh, independent key is required for each message.

### C. Unit 2: Computational Security

1. **Computational Security vs. Perfect Security:**
    
    - **Perfect Security:** No bounds on adversary computation/running time.
    - **Computational Security:** Protects only against adversaries that have **bounded running time** (Probabilistic Polynomial Time, PPT). The probability of breaking security is negligible ($\text{negl}(n)$).
    - **Proof Techniques:** Computational security relies on Proof by Reduction.
2. **Pseudorandom Generator (PRG):**
    
    - **Purpose:** To take a short, truly random seed $s$ and output a longer, random-looking string $y$. It allows generating many pseudorandom keys from one short, truly random key.
    - **Definition:** A PRG $G$ is secure if no PPT distinguisher $D$ can tell its output $G(s)$ apart from a truly random string $y$ with non-negligible probability.
    - **Limitation:** A PRG is a "one-time shot"; a seed can be used only once to maintain security.
3. **Pseudorandom Function (PRF):**
    
    - **Purpose:** A keyed function $F_k(x)$ that can be evaluated on many inputs $x$ (and thus provides many outputs) given one single secret key $k$.
    - **Definition:** A keyed function $F$ is a PRF if a PPT distinguisher $D$ cannot distinguish the oracle output $F_k(\cdot)$ (for a random key $k$) from the output of a Truly Random Function $T_F(\cdot)$ with non-negligible advantage. The distinguisher interacts adaptively with the oracle.
    - **Construction:** PRFs can be constructed by repeatedly using a PRG (e.g., Goldreich-Goldwasser-Micali (GGM) construction).

### D. Unit 3: CPA Security for Encryption

1. **Chosen Plaintext Attack (CPA):** In the CPA setting, the adversary might know the messages encrypted in many ciphertexts, or can decide some of the messages that will be encrypted (the training phase).
2. **CPA Security Goal:** For any ciphertext $C$ where the adversary does not already know the plaintext, the adversary cannot tell if the message encrypted in $C$ is $m_0$ or $m_1$ (where $m_0, m_1$ are known to the adversary).
3. **Formal CPA Security (Priv$_{\mathcal{A},\Pi}(n)$ Game):** An encryption scheme $\Pi$ is CPA secure if for any PPT adversary $A$, the probability that $A$ wins the CPA game is negligibly close to $1/2$.
4. **Insecure Example:** A simple permutation encryption scheme where $C = F_k(M)$ (using a PRF or PRP) is **not** CPA secure because encrypting the same message twice results in the same ciphertext. This allows the adversary to trivially distinguish in the challenge phase.
5. **CPA Secure Construction from PRF:** A scheme can be constructed using a PRF $F$ where the ciphertext $C$ contains the output of $F$ combined with the message, ensuring different ciphertexts are produced even for the same message.

---

## II. Examples of Proof Construction

Cryptographic proofs typically use either a **Reduction** (to prove security, showing that breaking the scheme implies breaking an underlying secure primitive) or an **Attack/Distinguisher** (to prove insecurity, showing an efficient method to violate the security definition).

### Example 1: Proof of Security via Reduction (PRF $F^1$)

**Problem:** Let $F: {0, 1}^n \times {0, 1}^n \to {0, 1}^n$ be a secure PRF. Construct $F^1_k(x) = F_k(x) \oplus x$. Is $F^1$ a secure PRF?

**Conclusion:** **Yes**, $F^1$ is a secure PRF.

**Logic Itemized (Proof by Contradiction and Reduction):**

1. **Setup and Assumption (Contradiction):**
    
    - **Assumption:** Assume $F$ is a secure PRF (i.e., it is indistinguishable from a truly random function $T_F$).
    - **Contradiction:** Assume $F^1$ is **not** secure. This means there exists a PPT distinguisher $A$ that can distinguish between $F^1$ and $T_F$ with a non-negligible advantage, $p(n)$.
    - $|\text{Pr}[A^{F^1_k(\cdot)} \to 1] - \text{Pr}[A^{T_F(\cdot)} \to 1]| = p(n)$.
2. **Reduction (Building a Distinguisher $D$):**
    
    - We construct a new PPT distinguisher $D$ whose goal is to break the security of the underlying PRF, $F$, by using $A$ as a subroutine.
    - $D$ has access to an oracle $O(\cdot)$ which is either $F_k(\cdot)$ or $T_F(\cdot)$.
3. **Algorithm $D^{O(\cdot)}(1^n)$:**
    
    - 1. Activate $A(1^n)$.
    - 2. Upon receiving query $x_i$ from $A$:
        
        - (a) Query the oracle to receive $y_i \leftarrow O(x_i)$.
        - (b) Compute $y'_i = y_i \oplus x_i$.
        - (c) Send $y'_i$ to $A$.
    - 3. Output whatever $A$ outputs.
4. **Analysis of $D$'s Advantage (Connecting Cases):**
    
    - **Case 1: $O = T_F$ (Truly Random Function):**
        - $D$ queries $T_F(x_i)$ and gets $y_i$, which is truly random.
        - $D$ calculates $y'_i = y_i \oplus x_i$. Xoring a truly random value ($y_i$) with anything ($x_i$) results in a random value ($y'_i$).
        - $D$ is perfectly simulating a Truly Random Function $T_F$ for $A$. Therefore, $\text{Pr}[D^{T_F} \to 1] = \text{Pr}[A^{T_F} \to 1]$.
    - **Case 2: $O = F_k$ (Pseudorandom Function):**
        - $D$ queries $F_k(x_i)$ and gets $y_i = F_k(x_i)$.
        - $D$ calculates $y'_i = F_k(x_i) \oplus x_i$. This is exactly the definition of $F^1_k(x_i)$.
        - $D$ is perfectly simulating the PRF $F^1_k$ for $A$. Therefore, $\text{Pr}[D^{F_k} \to 1] = \text{Pr}[A^{F^1_k} \to 1]$.
5. **Conclusion:**
    
    - $D$'s advantage in distinguishing $F$ from $T_F$ is equal to $A$'s non-negligible advantage $p(n)$: $|\text{Pr}[D^{F_k} \to 1] - \text{Pr}[D^{T_F} \to 1]| = |\text{Pr}[A^{F^1_k} \to 1] - \text{Pr}[A^{T_F} \to 1]| = p(n)$.
    - This contradicts the initial assumption that $F$ is a secure PRF. Therefore, the original premise (that $F^1$ is not secure) must be false, and **$F^1$ is a secure PRF**.

### Example 2: Proof of Insecurity via Distinguisher (PRF $F^2$)

**Problem:** Let $F: {0, 1}^n \times {0, 1}^n \to {0, 1}^n$ be a secure PRF. Construct $F^2_k(x_1 | x_2) = (F_k(x_1) \oplus x_2) | F_k(x_2)$. Is $F^2$ a secure PRF?

**Conclusion:** **No**, $F^2$ is not a secure PRF.

**Logic Itemized (Attack via Distinguisher):**

1. **Setup and Goal:**
    
    - **Theorem:** $F^2$ is not a PRF.
    - **Goal:** Construct a PPT distinguisher $D$ that can distinguish between the oracle $O(\cdot)$ running $F^2_k(\cdot)$ or a truly random function $T_F(\cdot)$ with a non-negligible advantage.
2. **Algorithm $D^{O(\cdot)}(1^{2n})$ (The Attack):**
    
    - 1. Set $x_1, x_2 = 0^n$ and query the oracle for $O(x_1 | x_2)$.
    - 2. Receive back $y = a | b$ (where $a$ is the first $n$ bits and $b$ is the last $n$ bits).
    - 3. If $a = b$ output $1$, else output $0$.
3. **Analysis of $D$'s Advantage (Calculating Probabilities):**
    
    - **Case 1: $O = F^2_k$ (The Insecure Function):**
        - The input queried is $x_1 | x_2 = 0^n | 0^n$.
        - The output $a | b$ is computed as: $a | b = (F_k(0^n) \oplus 0^n) | F_k(0^n) = F_k(0^n) | F_k(0^n)$.
        - Since $a = F_k(0^n)$ and $b = F_k(0^n)$, we have $a=b$ with probability 1.
        - Therefore, $D$ outputs $1$ with probability 1: $\text{Pr}[D^{F^2} \to 1] = 1$.
    - **Case 2: $O = T_F$ (Truly Random Function):**
        - The output $a | b$ comes from a truly random function.
        - $a$ and $b$ are two random bit strings of length $n$.
        - The probability that two random strings of length $n$ are equal is $1/2^n$.
        - Therefore, $D$ outputs $1$ with probability $1/2^n$: $\text{Pr}[D^{T_F} \to 1] = 1/2^n$.
4. **Conclusion:**
    
    - The distinguishing advantage of $D$ is $1 - 1/2^n$.
    - $|1 - 1/2^n|$ is a non-negligible probability.
    - Since $D$ can distinguish the output of $F^2$ from a truly random function with non-negligible advantage, **$F^2$ is not a secure PRF**.