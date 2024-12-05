# Diabolo: a Tool for Bounding the Posterior Distributions of Discrete Probabilistic Programs with Loops

**Diabolo** (**Di**screte **Di**stribution **A**analysis via **Bo**unds, supporting **l**oops and **o**bservations) is an implementation of the two methods in

> Zaiser, Murawski, Ong: *Guaranteed Bounds on Posterior Distributions of Discrete Probabilistic Programs with Loops.* [(POPL 2025)](https://doi.org/10.1145/3704874) [(arxiv version)](https://arxiv.org/abs/2411.10393)

Diabolo can run both semantics from the paper (Residual Mass Semantics & Geometric Bound Semantics).
It also includes the benchmarks from the paper and scripts to reproduce the reported data (i.e. plots and tables).
The code and benchmarks are [archived on Zenodo](https://doi.org/10.5281/zenodo.14169507), where a VirtualBox image is included to avoid issues with dependencies.



## Installation

Diabolo is written in Rust and can be built with Cargo as usual.

1. Clone the repository:
   ```shell
   git clone https://github.com/fzaiser/diabolo.git
   cd diabolo
   ```
2. Install Rust. [Rustup](https://rustup.rs/) is the recommended way of doing this.
3. Install the dependencies Z3, CBC, and IPOPT (see below)
4. Build the project:
   ```shell
   cargo run --release --bins
   ```
   This may take a minute or two.
   The build results is then available at `target/release/residual` (for the Residual Mass Semantics) and `target/release/geobound` (for the Geometric Bound Semantics).

### Installing dependencies on Ubuntu

If you are running Ubuntu, you can just run the following commands to install the dependencies (tested on Ubuntu 22.04):

```shell
sudo apt install build-essential libclang-dev z3 libz3-dev coinor-cbc coinor-libcbc-dev coinor-libipopt-dev
```

### Installing dependencies on MacOS

If you are running MacOS, you can probably install the dependencies via [Homebrew](https://brew.sh/), but this has not been tested.

### Manual dependency installation

On other systems, install the dependencies by following their installation instructions:

- [**Z3**](https://github.com/Z3Prover/z3): an SMT solver
- [**CBC**](https://github.com/coin-or/Cbc): a linear programming solver ([instructions for installing library files](https://github.com/KardinalAI/coin_cbc?tab=readme-ov-file#prerequisites-installing-cbc-library-files))
- [**IPOPT**](): a nonlinear solver ([installation instructions](https://coin-or.github.io/Ipopt/INSTALL.html))
- As an **alternative** for CBC and IPOPT: try [coinbrew](https://coin-or.github.io/coinbrew/)



## Trying out Diabolo

To try out Diabolo after the installation, you can run it on a simple example, e.g. on the Die Paradox example from the paper (`diabolo/benchmarks/die_paradox.sgcl`).

```shell
target/release/residual benchmarks/die_paradox.sgcl -u 30
```

This runs the Residual Mass Semantics with unrolling limit 30 and should produce the following output:

```
[...]

Probability masses:
p(0) = 0
p(1) ∈ [0.6666666666666374, 0.6666666666667347]
p(2) ∈ [0.2222222222222125, 0.22222222222228374]
p(3) ∈ [0.07407407407407082, 0.07407407407413344]
p(4) ∈ [0.024691358024690278, 0.024691358024750004]
p(5) ∈ [0.00823045267489676, 0.008230452674955523]
p(6) ∈ [0.002743484224965586, 0.00274348422502403]
p(7) ∈ [0.0009144947416551954, 0.0009144947417135321]
p(8) ∈ [0.00030483158055173183, 0.00030483158061003285]
p(9) ∈ [0.00010161052685057727, 0.00010161052690886643]
p(10) ∈ [0.00003387017561685909, 0.0000338701756751443]
p(11) ∈ [0.00001129005853895303, 0.00001129005859723692]
p(12) ∈ [3.763352846317677e-6, 3.7633529046011254e-6]
p(13) ∈ [1.2544509487725588e-6, 1.2544510070558613e-6]
p(14) ∈ [4.181503162575196e-7, 4.1815037454077305e-7]
p(15) ∈ [1.3938343875250653e-7, 1.3938349703574368e-7]
p(16) ∈ [4.6461146250835515e-8, 4.6461204534067224e-8]
p(17) ∈ [1.5487048750278505e-8, 1.5487107033508404e-8]
p(18) ∈ [5.162349583426168e-9, 5.1624078666554656e-9]
p(19) ∈ [1.7207831944753894e-9, 1.7208414777044853e-9]
p(n) ∈ [0.0, 5.828322899542719e-14] for all n >= 20

Moments:
0-th (raw) moment ∈ [0.9999999999999417, 1.0000000000000584]
1-th (raw) moment ∈ [1.49999999999949, inf]
2-th (raw) moment ∈ [2.9999999999863034, inf]
3-th (raw) moment ∈ [8.249999999585205, inf]
4-th (raw) moment ∈ [29.99999998732587, inf]
Total time: 0.01112s
```

As you can see, the Residual Mass Semantics found bounds on the probability masses and lower bounds on the moments, but cannot find nontrivial upper bounds on the moments.
For the latter, we need the Geometric Bound Semantics:

```shell
target/release/geobound benchmarks/die_paradox.sgcl -u 30 --objective ev
```

This command runs the Geometric Bound Semantics with an unrolling limit of 30 and instructs Diabolo to optimize the bounds of the expected value (`ev`).
It produces some more detailed output with information about the constraint generation, solution, and optimization process, which ends with:

```
[...]

Probability masses:
p(0) = 0.0
p(1) ∈ [0.6666527630456288, 0.6666770943633902]
p(2) ∈ [0.22221758768187627, 0.22222743618884813]
p(3) ∈ [0.07407252922729209, 0.07407668111652053]
p(4) ∈ [0.02469084307576403, 0.02469266157548205]
p(5) ∈ [0.008230281025254676, 0.008231104465076538]
p(6) ∈ [0.0027434270084182254, 0.002743810127447624]
p(7) ∈ [0.0009144756694727419, 0.0009146576965923832]
p(8) ∈ [0.0003048252231575806, 0.00030491305986845587]
p(9) ∈ [0.00010160840771919354, 0.00010165126743302656]
p(10) ∈ [0.00003386946923973118, 0.00003389054637013827]
p(11) ∈ [0.000011289823079910394, 0.00001130024414662526]
p(12) ∈ [3.7632743599701313e-6, 3.768445765672778e-6]
p(13) ∈ [1.2544247866567103e-6, 1.2569974662109285e-6]
p(14) ∈ [4.181415955522368e-7, 4.19423603857774e-7]
p(15) ∈ [1.393805318507456e-7, 1.4002009699349777e-7]
p(16) ∈ [4.646017728358187e-8, 4.6779482591927495e-8]
p(17) ∈ [1.5486725761193953e-8, 1.5646220531204762e-8]
p(18) ∈ [5.162241920397985e-9, 5.241937279120439e-9]
p(19) ∈ [1.7207473067993283e-9, 1.7605779449585905e-9]
[...]

Asymptotics: p(n) <= 0.000031278015409592094 * 0.5000113414027128^n for n >= 31

Moments:
0-th (raw) moment ∈ [0.9999791445684384, 1.0000208558665198]
1-th (raw) moment ∈ [1.4999687168525118, 1.5000417126793457]
2-th (raw) moment ∈ [2.9999374337005067, 3.0001251418274286]
3-th (raw) moment ∈ [8.2498279425375, 8.250542299060541]
4-th (raw) moment ∈ [29.99937433224882, 30.003128755450366]
```

In particular, it finds the bound `[1.4999687168525118, 1.5000417126793457]` on the expected value (i.e. the `1-th (raw) moment`).

You can also optimize the asymptotic tail bound instead (for this, no unrolling is needed):

```
$ target/release/geobound benchmarks/die_paradox.sgcl --objective tail
[...]
Asymptotics: p(n) <= 764.9018711960184 * 0.33874380844278107^n for n >= 9
[...]
Total time: 0.15064 s
```

As you can see, the asymptotic tail bound of `O(0.33874380844278107^n)` is much better than the asymptotic tail bound of `O(0.5000113414027128^n)` obtained above with a different optimization objective.

All the flags that Diabolo accepts are documented in the help text (`target/release/geobound --help`).
More information on how to use Diabolo can be found further down.
As a starting point for experimentation, have a look at the `*.sgcl` files in `benchmarks`, e.g. `benchmarks/geo.sgcl`.



## How to use Diabolo

Diabolo consists of two binaries (created in `target/release` after running `cargo build --release --bins`): `residual` and `geobound`.

The `residual` binary implements the Residual Mass Semantic (Section 3 in the paper).
It computes bounds on probability masses of the posterior distribution by unrolling all loops a number of times and bounding the remaining probability mass.
It takes the following command-line arguments:

* `<filename>.sgcl`: a file containing the probabilistic program to analyze.
  The probabilistic programming language is described below.
* `-u <number>` or `--unroll <number>` (default: 8): the loop unrolling limit (i.e. number of times each loop is unrolled).
  Higher values take longer, but yield more precise results.
* `--limit <number>`: the limit up to which probability mass bounds are output, e.g. `--limit 50` outputs `p(0), ..., p(50)`.

The `geobound` binary implements the Geometric Bound Semantics (Section 4 and 5 in the paper).
It computes a global bound on the program distribution that takes the form of an EGD (eventually geometric distribution).
In order to find such an EGD bound, it needs to synthesize a contraction invariant, a problem that the Geometric Bound Semantics reduces to a system of polynomial inequality constraints.
Typically, we do not just want any solution to this constraint problem, but one that minimizes a certain bound, e.g. the expected value or the tail asymptotics.
If such an objective is specified, Diabolo will try to minimize this objective.
It takes the following command-line arguments:

* `<filename>.sgcl`: a file containing the probabilistic program to analyze.
  The probabilistic programming language is described below.
* `-u <number>` or `--unroll <number>` (default: 8): the loop unrolling limit (i.e. the number of times each loop is unrolled).
  Higher values take longer, but (usually) yield more precise results (unless the solver encounters numerical issues).
* `-d <number>` (default: 1): the size of the contraction invariant to be synthesized.
  The default of 1 is usually fine, but some programs only admit larger contraction invariants.
  Increasing this value can also sometimes improve the bounds.
* `--objective <objective>` (default: none): the bound to minimize.
  It can be one of the following:
  * `total`: the total probability mass
  * `ev`: the expected value
  * `tail`: the tail asymptotic bound, i.e. the `c` in `O(c^n)` where `p(n) = O(c^n)` as `n` grows.
* `--solver <solver>` (default: `ipopt`): the solver to use for the constraint problem.
  The following solvers are available:
  * `ipopt`: the IPOPT solver. This is a fast numerical solver and almost always the best option.
  * `z3`: the Z3 SMT solver. This is an exact solver but only works for small programs and low unrolling limits.
    On the upside, it can (in principle) prove infeasibility, i.e. that no bound exists for the given invariant size.
* `--optimizer <optimizer>` (default: `ipopt adam-barrier linear`): a list of optimizers to minimize the objective.
  The optimizers are run in the order they are specified, e.g. `--optimizer ipopt --optimizer linear`.
  The following optimizers are available:
  * `ipopt`: the IPOPT solver. This is a fast numerical optimizer and should usually be included.
  * `linear`: a linear programming solver (uses COIN-CBC).
    This is an extremely fast way to optimize the linear variables of the constraint problem.
    It should usually be included, but is not enough on its own, as it does not touch the nonlinear variables.
  * `adam-barrier`: a solver provided by us that combines the barrier method with the ADAM optimizer.
    This is usually the slowest solver, so it is best omitted in some cases by passing `--optimizer ipopt --optimizer linear`.
    However, it is typically useful for `--objective tail`, which is why it is included in the default.
* `--keep-while`: deactivates the usual transformation of `while` loops into `do-while` loops.
  By default, the semantics conceptually treats `while <event> { ... }` as `if <event> { do { ... } while <event> }`.
  (But note that this is not valid syntax!)
  Occasionally, it can be useful to deactivate this transformation, which is what `--keep-while` does.



## Probabilistic programming language (SGCL)

The syntax of a probabilistic program is as follows:

```
<statements>

return X;
```

where `X` is the variable whose posterior distribution will be computed and `<statements>` is a sequence of statements.

**Statements** take one of the following forms (where `c` is a natural number):

* constant assignment: `X := c`
* incrementing: `X += c;` or `X += Y;` (the latter is only supported for `Y` with finite support)
* decrementing: `X -= c;` where `c` is a natural number
* sampling: `X ~ <distribution>;` where `<distribution>` is `Bernoulli(p)`, `Categorical(p0, p1, ..., pn)` or `Geometric(p)`
* observations: `observe <event>` where `<event>` is described below. `fail` means observing an event that happens with probability 0.
* branching: `if <event> { <statements> } else { <statements> }` where `<event>` is described below
* looping: `while <event> { <statements> }`

**Distributions**: The following distributions are supported (where `m`, `n` are natural numbers, `a`, `b` are rational numbers and `p` is a rational number between 0 and 1).
Rational numbers can be written as decimals (e.g. `0.4`) or fractions (e.g. `355/113`).

* `Bernoulli(p)`
* `Categorical(p_0, p_1, ..., p_n)`: categorical distribution on `{0, ..., n}` where `i` has probability `p_i`
* `UniformDisc(a, b)`: uniform distribution on `{a, ..., b - 1}`
* `Geometric(p)`

**Events** take one of the following forms:

* `n ~ <distribution>`: the event of sampling `n` from the given distribution
* `flip(p)`: happens with probability `p` (short for `1 ~ Bernoulli(p)`)
* `X in [n1, n2, n3, ...]`: the event of `X` being in the given list of natural numbers
* `X not in [n1, n2, n3, ...]`
* `X = n`, `X != n`, `X < n`, `X <= n`, `X > n`, `X >= n` for a variable `X` and a natural number `n`
* `not <event>`: negation/complement
* `<event> and <event>`: conjunction/intersection
* `<event> or <event>`: disjunction/union



## Organization of the source code

The source code is organized as follows:

- `benchmarks/`: directory containing the benchmarks and benchmark scripts
  - `ours/`: new benchmarks that we contributed (some are adapted from existing benchmarks)
  - `output/`: directory for GuBPI output files
  - `outputs/`: directory for output of our tool (for Fig. 7 in the paper)
  - `plots/`: plotting scripts (for Fig. 7)
  - `polar/`: benchmarks from the Polar repository
  - `prodigy/`: benchmarks from the Prodigy repository
  - `psi/`: benchmark from the PSI repository
- `dependencies/`: two Rust dependencies which had to be patched
- `src/`: source code of our implementation
  - `bin/`: binaries
    - `geobound.rs`: code for the `geobound` binary implementing the Geometric Bound Semantics
    - `residual.rs`: code for the `residual` binary implementing the Residual Mass Semantics
    - `stats.rs`: code for an auxiliary `stats` binary to report information about a probabilistic program (e.g. number of variables)
  - `bound/`: for data structures implementing bounds on distributions
    - `egd.rs`: implements EGDs (eventually geometric distributions)
    - `finite_discrete.rs`: implements finite discrete distributions
    - `geometric.rs`: implements a geometric bound, aggregating a finite discrete lower bound and an EGD upper bound
    - `residual.rs`: implements a residual mass bound, consisting of a finite discrete lower bound and the residual mass
  - `number/`: for number types
    - `f64.rs`: an extension of double-precision floating point numbers.
      Floating point numbers are used in some places to speed up the computations.
      But all results are verified with rational number computations.
    - `float_rat.rs`: for storing a rational number and its closest floating point number
    - `number.rs`: traits for number types
    - `rational.rs`: rational number type
  - `semantics/`: implementations of the transformer semantics from the paper
    - `geometric.rs`: the geometric bound semantics, which generates polynomial inequality constraints
    - `residual.rs`: the residual mass semantics
    - `support.rs`: for overapproximating the support of variables
  - `solvers/`: implementations of the solvers and optimizers for the polynomial inequality constraints
    - `adam.rs`: the `adam-barrier` solver that combines the barrier method with the popular ADAM algorithm
    - `ipopt.rs`: to run the IPOPT solver
    - `linear.rs`: to run an LP solver (COIN-CBC) to optimize the linear variables of the optimization problem
    - `problem.rs`: data structure for the constraint problem
    - `z3.rs`: to run the Z3 SMT solver
  - `interval.rs`: an interval data type
  - `parser.rs`: for parsing SGCL programs
  - `ppl.rs`: data structures for constructs in the PPL (probabilistic programming language)
  - `support.rs`: data structures for the support set of program variables
  - `sym_expr.rs`: data structures for symbolic expressions and constraints
  - `util.rs`: contains miscellaneous functions
  - `test.py`: a script to test changes during development
