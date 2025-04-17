Elephant collision rate: defined as the number of buckets mapped by more than one elephant flows divided by the total
4 ∥x ∥1 is the first moment of vector x, i.e.,  ∥x ∥1  = Σ xi .


number of buckets. It is proved that the number of elephant flows that mapped to each bucket follows a Binomial distri- bution in the literature [48]. We show only the following formula of the elephant collision rate Phc, and the detailed proof is provided in Section A.1 of our technical report [47].

where H is the number of elephant flows and w is the num- ber of buckets. For example, when H/w  = 0.1 or 0.01, the elephant collision rate is 0.0046 and 0.00005, respectively.
Solutions for elephant collisions: Obviously, reducing the hash collision rate can reduce the elephant collision rate. Thus, we use two classic methods [49–57]: 1) by using multi- ple sub-tables (see Section4.2); 2) by using multiple key-value pairs in one bucket (see Section 4.3).
In this section, we show some details those are not provided in the submission due to space limitation, including detailed derivation proofs and reasons of some claims.
A.1   Probability of Elephant Collisions
Theorem A.1.  Within any bucket in the heavy part of the Elastic sketch, the probability of elephant collisions is

where H is the number of elephant flows, and w is the number of buckets in the heavy part.
Proof. There are totally H elephant flows, and each flow is randomly mapped to a certain bucket by the hash function. Given an arbitrary bucket and an arbitrary flow, the proba- bility that the flow is mapped to the bucket is  . Therefore, for any bucket, the number of elephant flows that mapped to the bucket Z follows a Binomial distribution B(H, ). When H is large (e.g., H > 100), and  is a small probability, then Z approximately follows a Poisson distribution π (), i.e.,
                       (5)
There are elephant collisions within one bucket iff Z ≥ 2 for this bucket. Therefore, we have
Phc  = 1 - Pr{Z = 0} - Pr{Z = 1}
                             (6)
□

A.2   Error Bound of the Elastic sketch
Now we derive the error bound of the Elastic sketch for the frequency estimation task. Here the Elastic sketch can be either the software version or the hardware version, and its adaptivity is not considered. The light part is a Count-min sketch, which is composed of d arrays, and each array has w counters.
Lemma A.2.  Suppose we use an Elastic sketch (Elastic) and a Count-min sketch (CM) to record a stream at the same time, and the CM sketch and the light part of Elastic have the same width and height. If we insert all the flows and the frequencies stored in the heavy part of Elastic Sketch into its light part, the light part is exactly the same as CM.
Proof. We give a brief explanation of this Lemma. After recording a stream, the heavy part in Elastic Sketch captures some items in the stream. If we insert the items from the heavy part into the light part, it is equivalent to reordering the items in the stream and then inserting them into a CM


sketch. According to the insertion of CM sketches, it is obvi- ous that the order of items does not affect the final state of
the CM sketch. As a result, Lemma A.2holds.                   □
Based on LemmaA.2, if we use an Elastic Sketch to record a stream, the stream can be seen as two sub-streams recorded by the two parts separately. We then have the following definition.
Definition A.3.  Let vector f = (f1, f2, ..., fn) denote the fre- quency vector for a stream, where fi denotes the frequency ofthe i-th flow. Then fh  and fl  denote the frequency vector of sub-streams recorded by the heavy part and the light part, respectively.
Now we give the error bound of Elastic sketch on fre- quency estimation task.
Theorem A.4.  Given two parameters ϵ and δ , let w = ⌈ ⌉
(e is Euler number) and d = ⌈ln ⌉ . Let Elastic Sketch with d
(d is the number of counter arrays) and w (w is the number of
counters in each array) record the stream with,. The reported
fˆi  ≤ fi  + ϵ ∥,l∥1 < fi  + ϵ ∥,∥1                        (7)
with probability at least 1 - δ .
eil t, hoi nce s lie 
the heavy part and the light part, respectively. We use the
“≤”operator because if the flag in the heavy part is not set,
 tflail.rlfi, ,
heavy part counts each flow exactly, we have f = fih. Since
according to [10], we have f ≤ ϵ ∥fl∥1 with probability at
least 1 - δ . Then, we have
fˆi  ≤ f + f ≤ fi  + ϵ ∥fl∥1                          (8)
with probability at least 1 - δ. Since fl  represents the stream stored in the light part, ∥fl∥1  < ∥f∥1. Therefore, TheoremA.4 holds.                                                                          □
Notice that in practice, the heavy part captures a large proportion of packets in the stream, especially for elephant flows, which means ∥fl∥2 /∥f∥2 is usually small. As a result, given an Elastic sketch and a Count-min sketch, if the di- mension of CM and the light part of Elastic Sketch is the same, our Elastic sketch has always a better theoretical error bound. Although Elastic Sketch needs an additional heavy part compared to CM, it has a smaller light part because we do not need large counters to accommodate the largest flow.







In other words, the error bound of Elastic Sketch is de- termined by the light part, so its adaptivity to flow size dis- tribution does not affect this error bound. In the next part, we will discuss how the adaptivity to available bandwidth affects the error bound.
A.3   Error Bound of the Compressed CM    Sketch using Maximum Compression
Consider a Count-min sketch with d arrays and w counters per array. According to the literature [10], we can easily get the error bound of the CM sketch

where ϵ is a given positive number, nj is the real size offlow
f j  and j  is the estimated size off j.
Next, we consider the CM sketch using our compression technique.
Theorem A.5. Assume that a CM sketch with d arrays and zw counters per array, and z  is the compression rate of the sketch. Given an arbitrary small positive number ϵ and an arbitrary flow fj, the error of the sketch after our compression is bounded by

Proof. After compression, each counter in the new sketch will be the maximum value of z counters in the original sketch. Because each array is independent with each other, we first only focus on the first array. For a certain flow fj, it is mapped to one counter, and the counter is in the same com- pression group with other z − 1 counters. For convenience, we use V1, V2 · · ·Vz  to denote the number of packets mapped to the z counters, excluding packets from flow fj. Without loss of generality, we assume that flow fj  is mapped to the first counter in the compression group. In this way, the estimated
size off j  in the first array () is
 = max(V1 + nj, V2, V3 , · · · , Vz)                (11)
And we have
Pr{ ≥ nj + ϵN}
=Pr{max(V1 + nj, V2, V3 , · · · , Vz) ≥ nj + ϵN}
=1 − Pr{max(V1 + nj, V2, V3 , · · · , Vz) < nj + ϵN}      (12)

According to Markov inequality, it is easy to derive that
Pr{V1 + nj  < nj  + ϵN} = Pr{V1 < ϵN}
             (13) 





Therefore, we have




Now we focus on the error bound for d arrays. Note that the estimated size offlow fj  is minimum value of d mapped counters, we have
j  = min( ,  , · · · , )                     (16)
where  denotes the estimated size of f j  in the ith  array.
Therefore, we have





	

(17)




□
A.4   Error Bound of CM sketches using SC Compression
Here we prove that the error bound does not change after using the SC Compression algorithm.
Theorem A.6. Assume that a CM sketch with d arrays and zw counters per array, and z is the SC compression rate of the sketch. Given an arbitrary small positive number ϵ , the error of any flow after compression is bounded by
                 (18)
Clearly that the error bound is identical to that of the CM sketch not using the SC Compression algorithm.







Proof. We first focus on one array of the CM sketch. Let flow fj  mapped to counter C1, and after compression, C1  is compressed into a new counter with other z − 1 counters C2, C3 · · ·Cz. Let Xi be the number of packets that mapped to counter Ci before compression (except for packets offlow fj). Therefore, after compression, the value in the new counter is X1 + X2 + · · · + Xz. Then the estimated flow size of flow f j in this array is
                          (19)
According to the Markov inequality, given a positive number ϵ , we have


             (20)

Because the estimated flow size off j is the minimum of the
estimated flow size in each array, i.e., j  = min{  · · · },
we have

□
A.5   Error Bound Comparison
In this subsection, We compare the error bound of the CM sketch before and after our maximum compression.
Theorem A.7.  Given a compressed CM sketch using our maximum compression, assume it has the same d and w with another standard CM sketch. The compressed CM sketch has a smaller error bound than the standard CM sketch.
Proof. For convenience, we use PCM  to denote the error bound of standard CM sketches, and use PMC  to denote the error bound of the compressed CM sketch using our maximum compression algorithm. First, we have

F(z) as
                         (23)


where z is a positive integer, and x is a number in [0, 1]. Ac- cording to the inequality of arithmetic and geometric means, we have

                       (24)

Therefore, F(z) is a monotonic increasing function, and we have

≤ [1 − F(1)]d                                          (25)
= [1 − (1 − x)]d  = xd
= PCM
Therefore, the compressed sketch has a smaller error bound than the standard CM sketch.                                        □
A.6   Proof of No Under-estimation Error
Theorem A.8. After using theMC algorithm, the CM sketch and the CU sketch still has only over-estimation error but no under-estimation error.
Proof. Without loss of generality, we only focus on one array (denoted as A[]) in the CM sketch or the CU sketch. For any flow fi  and its mapped counter in the array, the value in the counter A[д(fi)] should be not smaller than ni , i.e., A[д(fi)%w] ≥ ni. Let the array after using compression algorithm be denoted as A′[]. After using compression algo- rithm with compression ratio z, then flow fi  is mapped to counter A′[д(fi)%(w/z)], and the value in the counter is the maximum value of the original z values. Therefore, we have
A[д(fi)%w] ≤ A′[д(fi)%(w/z)]              (26)
In this way, we have n i   ≤ A′[д(fi)%(w/z)]. Therefore, for any flow, its estimated value in one array is not smaller than its real flow size.        
