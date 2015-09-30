# September 29, 2015

## Data compression

Find compact representations of data

Data compression **standards**: `jpeg`, `mp3`, `mpeg2`, `gzip`, `bzip2`

All of the above use the **Huffman algorithm** as a basic building block.

  - *Chromosome maps*: sequences of hundreds of millions of bases (symbols from *{A,C,G,T}).

  - **Goal**: store a chromosome map with 200 million bases.

*How do we represent a chromosome map?*

  - *Encode* every symbol that appears in the sequence *separately* by a **fixed length binary string**.

  - *Codeword c(x)* for symbol *x*: a binary string encoding *x*.

### Alphabet *A = {A,C,G,T}* with 4 symbols

Encode each symbol with 2 bits

|alphabet symbol *x*|codeword *c(x)*|
|:-----------------:|:-------------:|
|         A         |       00      |
|         C         |       01      |
|         G         |       10      |
|         T         |       11      |

**Output of encoding**: the concatenation of the codewords for every symbol in the input sequence.

**Example**:

  - Input sequence: *ACGTAAA*

  - Output: *c(A)c(C)c(G)c(T)c(A)c(A) = 000110110000*

  - Total length of encoding: 6 * 2 = 12 bits


## Symbol codes and optimal lossless compression

** Symbol code**: a set of codewords where every input symbol is encoded *separately*.

Examples of symbol codes

  - `C_0 = {00,01,10,11}` is a symbol code for *{A,C,G,T}*

  - ASCII encoding system: every character and special symbol on the computer keyboard is encoded by a different 7-bit binary string.

**Remark**: *C0 and ASCII are fixed-length symbol codes: each codeword has the same length.*

### Unique decodability

Decoding `C_0`:

  1. read 2 bits of the output

  2. print the symbol corresponding to this codeword;

  3. continue with the next 2 bits.

** Remark **:

  - *This decoding algorithm works for ASCII (replace 2 by 7)*

  - *`C_0`, ASCII: **distinct input sequences** have **distinct encodings**.*

#### Definition: A symbol code is *uniquely decodable* if, for any two *distinct* input sequences, their encodings are distinct.

  - **Lossless compression**: compress and decompress without errors.

  - Uniquely decodable codes allow for lossless compression.

  - A symbol code achieves *optimal lossless compression* when it produces an encoding of *minimum length* for its input (among all uniquely decodable symbol codes).

  - Huffman algorithm: provides a symbol code that achieves **optimal** lossless compression.

### Fixed-length vs variable codes

|alphabet symbol *x*|frequency *freq(x)*|
|:-----------------:|:-----------------:|
|         A         |   110 million     |
|         C         |   5 million       |
|         G         |   25 million      |
|         T         |   60 million      |

  - *A* appears much more often than the other symbols: it might be best to encode *A* with fewer bits.

  - Unlikely that the *fixed-length encoding* `C_0` is optimal.

### Prefix codes

Variable-length encodings:

#### C1

|alphabet symbol *x*|codeword *c(x)*    |
|:-----------------:|:-----------------:|
|         A         |        0          |
|         C         |        00         |
|         G         |        10         |
|         T         |        1          |

#### C2

|alphabet symbol *x*|codeword *c(x)*|
|:-----------------:|:-----------------:|
|         A         |        0          |
|         C         |        110        |
|         G         |        111        |
|         T         |        10         |

`C_2` is such that no code is a prefix of another.

*S* is a prefix of *t* if *s+s' = t*

#### Definition: A symbol is a prefix code if no codeword is a prefix of another.

### Decoding prefix codes

  1. Scan the binary string from left to right until you've seen enough bits to match a codeword.

  2. Output the symbol corresponding to this codeword.

    - Since no other codeword is a prefix of this codeword or contains it as a prefix, this sequence of bits cannot be used to encode any other symbol.

  3. Continue starting from the next bit of the string.

Thus prefix codes allow for

  - *unique decoding*;

  - *fast decoding* (the end of a codeword is instantly recognizable)

Examples of prefix codes: `C_0`, `C_2`

### Prefix codes and optimal lossless compression

  - Decoding a prefix code is fast.

    * Would like to focus on prefix codes (rather than **all** uniquely decodable symbol codes) for acheiving *optimal lossless compression*.

  - Information theory guarantees that.

  - So we can solely focus on prefix codes for optimal compression.

#### Compression gains from variable-length prefix codes

Chromosome map: *do we gain anything by using C2 isntead of C0 when compressing the map of 200 million bases?*

  - `C_0`: 2 bits x 200 million symbols = 400 million bits

  - `C_2`: 1 x 110 + 3 x 5 + 3 x 25 + 2 x 60 = 320 million bits

  - Improvement of 20% in this example.

### The optimal prefix code problem

**Input**:

  - Alphabet *A = {a1,...,an}*

  - Set *P = {p1,...,pn} of probabilies over *A* such that *pi = Pr[ai]*.

**Output**: a binary prefix code *C' = {c(a1),...,C(an)}* for *(A,P)* where codeword *c(ai)* has length *li* and the expected length of the code

*L(C') = sum over ai in A of pi x li*

is **minimum** among all prefix codes.

For chromosome example:

  - *L(C0) = 2*

  - *L(C2) = 1.6*

  - Coming up: *C2* is the output of the Huffman algorithm, hence an optimal encoding for *(A,P)*.

## Prefix codes and trees

  - A **binary tree** *T* is a rooted tree such that each node that is not a leaf has at most two children.

  - Binary tree for a prefix code: a branch to the left represents a 0 in the encoding and a branch to the right a 1.

  1. *Where do alphabet symbols appear in the tree?* the **leaf nodes** (without children, otherwise it would be a prefix)

  2. *What do codewords correspond to in the tree?* **root-to-leaf paths**

    - Recall that *li* is the length of the codeword *c(ai)* for input symbol *ai*. Therefore, *li* corresponds to the depth of *ai* in *T* (we assume that the root is at depth 0).

    - Can rewrite the expected length of the prefix code as:

```
L(C) = sum over ai in A of pi x li = sum over i in n of pi x depth(ai) = L(T)
```

  3. *Consider the tree corresponding to the optimal prefix code. Can it have internal nodes with one child?* No, optimal tree must be full: all internal nodes must have exactly two children (otherwise the node with one child would be a prefix)

### Claim:*There is an optimal prefix code, with corresponding tree T', in which the two lowest frequency characteres are assigned to leaves that are siblings in T' at maximum depth*.

#### Proof
By an *exchange argument*: start with a tree for an optimal prefix code and *transform* it into T'.

Let *T* be the tree for the optimal prefix code. Let *a, b* be the two symbols with the smallest probabilities, that is, `Pr[a] <= Pr[b] <= Pr[s]` for all `s in A - {a,b}`. Let *x* and *y* be the two siblings at maximum depth in *T*.

```
L(T)−L(T′) = sum ai ∈A Pr[ai] · depthT (ai) − sum ai ∈A Pr[ai] · depthT′(ai)
 = Pr[α] · depthT (α) + Pr[x] · depthT (x)
  − Pr[α] · depthT ′ (α) − Pr[x] · depthT ′ (x)
 = Pr[α] · depthT (α) + Pr[x] · depthT (x)
  − Pr[α] · depthT (x) − Pr[x] · depthT (α)
 = (Pr[α] − Pr[x]) · (depthT (α) − depthT (x)) ≥ 0
```

  - The third equality follows from the exchange

  - Similarly, exchanging *b* and *y* in *T'* yields *L(T') - L(T'') >= 0*

  - Hence *L(T) - L(T'') >= 0

  - Since *T* is optimal, it must be *L(T) = L(T'')*.

  - So *T''* is also optimal

The claim follows by setting *T^* to *T''*

### Building the optimal tree

Claim 1 tells us how to build the optimal tree greedily!

  1. Find the two symbols with the lowest probabilities.

  2. Remove them from the alphabet and replace them with a new meta-character with probability equal to the sum of their probabilities.

    - Idea: this meta-character will be the parent of the two deleted symbols in the tree.

  3. Recursively construct the optimal tree using this process.

**Greedy algorithms**: make a local (myopic) decision at every step that optimizes some criterion.

```
Huffman(A, P)
  if |A| = 2 then
    Encode one symbol using 0 and the other using 1
  end if
  Let α and β be the two symbols with the lowest probabilities Let ν be a new meta- character with probability Pr[α] + Pr[β] Let A1=A−{α,β}+{ν}
  Let P1 be the new set of probabilities over A1
  T1 = Huffman(A1, P1)
  return T as follows: replace leaf node ν in T1 by an internal node, and add two children labelled α and β below ν.
```

**Example**:

with *A = {A,C,G,T}* ({110/200, 5/200, 25/200, 60/200})

Remove *C, G*, giving *A' = {A,v0,T}* ({110/200, 30/200, 60/200})

Remove *v0, T*, giving *A'' = {A,v1}* ({110/200, 90/200})

Now there are only two left. Create a root with children *A*, *v1*. Replace node *v1* with a new node with two children: *v0* and *T*. Then replace the node holding *v0* with a node with two children: *C* and *G*.

Another way: put symbols on a vertical line,

```
A 110/200                               -> 0
C   5/200 -> 0
G  25/200 -> 1 -> 30/100 -> 0
T  60/200                -> 1 -> 90/100 -> 1 -> 100/100
```

**Proof**: by induction on the size of the alphabet n ≥ 2.

  - Base case. For n = 2, Huffman is optimal.

  - Hypothesis. Assume that Huffman returns the optimal prefix code for alphabets of size n.

  - Induction Step. Let A be an alphabet of size n + 1, P the corresponding set of probabilities.

Let T1 be the tree returned by our algorithm for (A1,P1), where A1, P1, T1 as in the pseudocode. By the hypothesis, T1 is optimal. Let T be the final tree returned for (A, P) by our algorithm. We claim that T is optimal.

We will prove the claim by contradiction. Suppose T∗ is the optimal tree for (A, P ) with L(T ∗) < L(T ).

W.l.o.g., suppose T∗ is an optimal tree where α, β appear as siblings at maximum depth (Claim 1 guarantees there is such an optimal tree.)

Replace α, β in T ∗ by a meta-character ν′ with Pr[ν′] = Pr[α] + Pr[β] to obtain the tree T1∗ on n nodes with expected length L(T1∗).

## Beyond Huffman coding

  - Huffman algorithm provides an optimal symbol code

  - Codes that encode larger blocks of input symbols might do better

  - Storage on noisy media: what if a bit of the output of the compressor is flipped?

    * decompression cannot carry through

    * need error correction on top of compression