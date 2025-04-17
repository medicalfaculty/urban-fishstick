# Elephant Collision Rate

Elephant collision rate is defined as the number of buckets mapped by more than one elephant flow divided by the total number of buckets. It is proved that the number of elephant flows that mapped to each bucket follows a Binomial distribution in the literature [48]. We show only the following formula of the elephant collision rate \( P_{hc} \), and the detailed proof is provided in Section A.1 of our technical report [47].

\[
P_{hc} = 1 - \left(1 + \frac{H(H-1)}{2w}\right) e^{-\frac{H}{w}}
\]

where \( H \) is the number of elephant flows and \( w \) is the number of buckets. For example, when \( H/w = 0.1 \) or \( 0.01 \), the elephant collision rate is \( 0.0046 \) and \( 0.00005 \), respectively.

### Solutions for Elephant Collisions

Obviously, reducing the hash collision rate can reduce the elephant collision rate. Thus, we use two classic methods [49–57]:
1. By using multiple sub-tables (see Section 4.2)
2. By using multiple key-value pairs in one bucket (see Section 4.3)

---

## Appendix

### A.1 Probability of Elephant Collisions

**Theorem A.1.** Within any bucket in the heavy part of the Elastic sketch, the probability of elephant collisions is:

\[
P_{hc} = 1 - \left(1 + \frac{H(H-1)}{2w}\right) e^{-\frac{H}{w}}
\]

where \( H \) is the number of elephant flows, and \( w \) is the number of buckets in the heavy part.

**Proof.** There are totally \( H \) elephant flows, and each flow is randomly mapped to a certain bucket by the hash function. Given an arbitrary bucket and an arbitrary flow, the probability that the flow is mapped to the bucket is \( \frac{1}{w} \). Therefore, for any bucket, the number of elephant flows that mapped to the bucket \( Z \) follows a Binomial distribution \( B(H, \frac{1}{w}) \). When \( H \) is large (e.g., \( H > 100 \)), and \( \frac{1}{w} \) is a small probability, then \( Z \) approximately follows a Poisson distribution \( \pi \left( \frac{H}{w} \right) \), i.e.,

\[
P(Z = k) \approx \frac{\left( \frac{H}{w} \right)^k e^{-\frac{H}{w}}}{k!}
\]

There are elephant collisions within one bucket iff \( Z \geq 2 \) for this bucket. Therefore, we have

\[
P_{hc} = 1 - P(Z = 0) - P(Z = 1)
\]

\[
P_{hc} = 1 - \left(1 + \frac{H}{w}\right) e^{-\frac{H}{w}}
\]

\(\blacksquare\)

### A.2 Error Bound of the Elastic Sketch

Now we derive the error bound of the Elastic sketch for the frequency estimation task. Here the Elastic sketch can be either the software version or the hardware version, and its adaptivity is not considered. The light part is a Count-min sketch, which is composed of \( d \) arrays, and each array has \( w \) counters.

**Lemma A.2.** Suppose we use an Elastic sketch (Elastic) and a Count-min sketch (CM) to record a stream at the same time, and the CM sketch and the light part of Elastic have the same width and height. If we insert all the flows and the frequencies stored in the heavy part of Elastic Sketch into its light part, the light part is exactly the same as CM.

**Proof.** We give a brief explanation of this Lemma. After recording a stream, the heavy part in Elastic Sketch captures some items in the stream. If we insert the items from the heavy part into the light part, it is equivalent to reordering the items in the stream and then inserting them into a CM sketch. According to the insertion of CM sketches, it is obvious that the order of items does not affect the final state of the CM sketch. As a result, Lemma A.2 holds. \(\blacksquare\)

Based on Lemma A.2, if we use an Elastic Sketch to record a stream, the stream can be seen as two sub-streams recorded by the two parts separately. We then have the following definition.

**Definition A.3.** Let vector \( \mathbf{f} = (f_1, f_2, \ldots, f_n) \) denote the frequency vector for a stream, where \( f_i \) denotes the frequency of the \( i \)-th flow. Then \( \mathbf{f}_h \) and \( \mathbf{f}_l \) denote the frequency vector of sub-streams recorded by the heavy part and the light part, respectively.

Now we give the error bound of Elastic sketch on frequency estimation task.

**Theorem A.4.** Given two parameters \( \epsilon \) and \( \delta \), let \( w = \lceil \frac{e}{\epsilon} \ln \frac{1}{\delta} \rceil \) (where \( e \) is Euler number) and \( d = \lceil \ln \frac{1}{\delta} \rceil \). Let Elastic Sketch with \( d \) (number of counter arrays) and \( w \) (number of counters in each array) record the stream. The reported \( \hat{f}_i \leq f_i + \epsilon \|\mathbf{f}_l\|_1 < f_i + \epsilon \|\mathbf{f}\|_1 \) with probability at least \( 1 - \delta \).

**Proof.** According to the query procedure of Elastic Sketch, the estimated value \( \hat{f}_i \) can be expressed as the sum of the heavy part and the light part. We use the “≤” operator because if the flag in the heavy part is not set, the light part will be queried. Since the heavy part counts each flow exactly, we have \( \hat{f}_i^h = f_i^h \). Since the light part of Elastic Sketch is a Count-min sketch for \( \mathbf{f}_l \), according to [10], we have \( \hat{f}_i^l \leq \epsilon \|\mathbf{f}_l\|_1 \) with probability at least \( 1 - \delta \). Then, we have:

\[
\hat{f}_i \leq f_i^h + \hat{f}_i^l \leq f_i + \epsilon \|\mathbf{f}_l\|_1
\]

with probability at least \( 1 - \delta \). Since \( \mathbf{f}_l \) represents the stream stored in the light part, \( \|\mathbf{f}_l\|_1 < \|\mathbf{f}\|_1 \). Therefore, Theorem A.4 holds. \(\blacksquare\)

Notice that in practice, the heavy part captures a large proportion of packets in the stream, especially for elephant flows, which means \( \|\mathbf{f}_l\|_1 / \|\mathbf{f}\|_1 \) is usually small. As a result, given an Elastic sketch and a Count-min sketch, if the dimension of CM and the light part of Elastic Sketch is the same, our Elastic sketch has always a better theoretical error bound. Although Elastic Sketch needs an additional heavy part compared to CM, it has a smaller light part because we do not need large counters to accommodate the largest flow.

---

## A.3 Error Bound of the Compressed CM Sketch using Maximum Compression

Consider a Count-min sketch with \( d \) arrays and \( w \) counters per array. According to the literature [10], we can easily get the error bound of the CM sketch:

\[
\text{Error} \leq \epsilon \|\mathbf{f}\|_1
\]

where \( \epsilon \) is a given positive number, \( n_j \) is the real size of flow \( f_j \), and \( \hat{n}_j \) is the estimated size of \( f_j \).

Next, we consider the CM sketch using our compression technique.

**Theorem A.5.** Assume that a CM sketch with \( d \) arrays and \( zw \) counters per array, and \( z \) is the compression rate of the sketch. Given an arbitrary small positive number \( \epsilon \) and an arbitrary flow \( f_j \), the error of the sketch after our compression is bounded by:

\[
\text{Error} \leq \epsilon \|\mathbf{f}\|_1
\]

**Proof.** After compression, each counter in the new sketch will be the maximum value of \( z \) counters in the original sketch. Because each array is independent with each other, we first only focus on the first array. For a certain flow \( f_j \), it is mapped to one counter, and the counter is in the same compression group with other \( z - 1 \) counters. For convenience, we use \( V_1, V_2, \ldots, V_z \) to denote the number of packets mapped to the \( z \) counters, excluding packets from flow \( f_j \). Without loss of generality, we assume that flow \( f_j \) is mapped to the first counter in the compression group. In this way, the estimated size of \( f_j \) in the first array \( \hat{n}_{1j} \) is:

\[
\hat{n}_{1j} = \max(V_1 + n_j, V_2, V_3, \ldots, V_z)
\]

And we have:

\[
P(\hat{n}_{1j} \geq n_j + \epsilon N) = P(\max(V_1 + n_j, V_2, V_3, \ldots, V_z) \geq n_j + \epsilon N)
\]

\[
= 1 - P(\max(V_1 + n_j, V_2, V_3, \ldots, V_z) < n_j + \epsilon N)
\]

According to Markov inequality, it is easy to derive that:

\[
P(V_1 + n_j < n_j + \epsilon N) = P(V_1 < \epsilon N)
\]

Therefore, we have:

\[
P(\hat{n}_{1j} \geq n_j + \epsilon N) \leq \frac{\mathbb{E}[V_1]}{\epsilon N}
\]

Now we focus on the error bound for \( d \) arrays. Note that the estimated size of flow \( f_j \) is the minimum value of \( d \) mapped counters, we have:

\[
\hat{n}_j = \min(\hat{n}_{1j}, \hat{n}_{2j}, \ldots, \hat{n}_{dj})
\]

where \( \hat{n}_{ij} \) denotes the estimated size of \( f_j \) in the \( i \)-th array. Therefore, we have:

\[
P(\hat{n}_j \geq n_j + \epsilon N) \leq \left( \frac{\mathbb{E}[V_1]}{\epsilon N} \right)^d
\]

\(\blacksquare\)

---

## A.4 Error Bound of CM Sketches using SC Compression

Here we prove that the error bound does not change after using the SC Compression algorithm.

**Theorem A.6.** Assume that a CM sketch with \( d \) arrays and \( zw \) counters per array, and \( z \) is the SC compression rate of the sketch. Given an arbitrary small positive number \( \epsilon \), the error of any flow after compression is bounded by:

\[
\text{Error} \leq \epsilon \|\mathbf{f}\|_1
\]

Clearly, the error bound is identical to that of the CM sketch not using the SC Compression algorithm.

**Proof.** We first focus on one array of the CM sketch. Let flow \( f_j \) mapped to counter \( C_1 \), and after compression, \( C_1 \) is compressed into a new counter with other \( z - 1 \) counters \( C_2, C_3, \ldots, C_z \). Let \( X_i \) be the number of packets that mapped to counter \( C_i \) before compression (except for packets of flow \( f_j \)). Therefore, after compression, the value in the new counter is \( X_1 + X_2 + \ldots + X_z \). Then the estimated flow size of flow \( f_j \) in this array is:

\[
\hat{n}_{ij} = X_1 + X_2 + \ldots + X_z
\]

According to the Markov inequality, given a positive number \( \epsilon \), we have:

\[
P(\hat{n}_{ij} \geq n_j + \epsilon N) \leq \frac{\mathbb{E}[X_1 + X_2 + \ldots + X_z]}{\epsilon N}
\]

Because the estimated flow size of \( f_j \) is the minimum of the estimated flow size in each array, i.e., \( \hat{n}_j = \min(\hat{n}_{1j}, \hat{n}_{2j}, \ldots, \hat{n}_{dj}) \), we have:

\[
P(\hat{n}_j \geq n_j + \epsilon N) \leq \left( \frac{\mathbb{E}[X_1 + X_2 + \ldots + X_z]}{\epsilon N} \right)^d
\]

\(\blacksquare\)

---

## A.5 Error Bound Comparison

In this subsection, we compare the error bound of the CM sketch before and after our maximum compression.

**Theorem A.7.** Given a compressed CM sketch using our maximum compression, assume it has the same \( d \) and \( w \) with another standard CM sketch. The compressed CM sketch has a smaller error bound than the standard CM sketch.

**Proof.** For convenience, we use \( P_{CM} \) to denote the error bound of standard CM sketches, and use \( P_{MC} \) to denote the error bound of the compressed CM sketch using our maximum compression algorithm. First, we have:

\[
F(z) = \left(1 - \left(1 - \frac{x}{z}\right)^z\right)^d
\]

where \( z \) is a positive integer, and \( x \) is a number in [0, 1]. According to the inequality of arithmetic and geometric means, we have:

\[
\left(1 - \frac{x}{z}\right)^z \geq e^{-x}
\]

Therefore, \( F(z) \) is a monotonic increasing function, and we have:

\[
P_{MC} \leq \left(1 - e^{-x}\right)^d \leq \left(1 - (1 - x)\right)^d = x^d = P_{CM}
\]

Therefore, the compressed sketch has a smaller error bound than the standard CM sketch. \(\blacksquare\)

---

## A.6 Proof of No Under-estimation Error

**Theorem A.8.** After using the MC algorithm, the CM sketch and the CU sketch still has only over-estimation error but no under-estimation error.

**Proof.** Without loss of generality, we only focus on one array (denoted as \( A[] \)) in the CM sketch or the CU sketch. For any flow \( f_i \) and its mapped counter in the array, the value in the counter \( A[\text{hash}(f_i)] \) should be not smaller than \( n_i \), i.e., \( A[\text{hash}(f_i) \% w] \geq n_i \). Let the array after using compression algorithm be denoted as \( A'[] \). After using compression algorithm with compression ratio \( z \), then flow \( f_i \) is mapped to counter \( A'[\text{hash}(f_i) \% (w/z)] \), and the value in the counter is the maximum value of the original \( z \) values. Therefore, we have:

\[
A[\text{hash}(f_i) \% w] \leq A'[\text{hash}(f_i) \% (w/z)]
\]

In this way, we have \( n_i \leq A'[\text{hash}(f_i) \% (w/z)] \). Therefore, for any flow, its estimated value in one array is not smaller than its real flow size. \(\blacksquare\)
