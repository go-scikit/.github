<p align="center"><img src="https://raw.githubusercontent.com/go-scikit/brand/main/social/go-scikit.png" alt="go-scikit" width="720"></p>

# go-scikit

🌐 **[Website](https://go-scikit.github.io)** · 📚 **[Documentation](https://go-scikit.github.io/docs/)**

**The scientific stack for Go, in pure Go (CGO=0): N-dimensional arrays, FFTs, numerical methods, statistics, labelled tables, figures and notebooks.**

go-scikit is the umbrella over seven organisations. Each is a library, or a
few, with its own site, documentation and release cycle; together they cover
what a Python notebook reaches for in NumPy, SciPy, pandas and matplotlib. No
cgo and no C libraries: one `go build` gives a static binary, and it
cross-compiles like any other Go program. Where a library is slower or less accurate than its
Python reference, its documentation says so.

## The organisations

| Organisation | What it is | Site | Docs |
|---|---|---|---|
| [**go-ndarray**](https://github.com/go-ndarray) | N-dimensional arrays in 13 dtypes with NumPy 2 promotion, broadcasting and strided views; `linalg` is `numpy.linalg`, `fft` is `numpy.fft`, `random` is `numpy.random` with NumPy's numbers for the same seed | [site](https://go-ndarray.github.io/) | [docs](https://go-ndarray.github.io/docs/) |
| [**go-fft**](https://github.com/go-fft) | the `numpy.fft` / `scipy.fft` equivalent: complex and real transforms of any length, N-D, DCT and DST, single precision, no FFTW | [site](https://go-fft.github.io/) | [docs](https://go-fft.github.io/docs/) |
| [**go-numerics**](https://github.com/go-numerics) | numerical methods: optimisation and root finding (`optimize`), quadrature and ODE solvers (`integrate`), special functions (`special`) | [site](https://go-numerics.github.io/) | [docs](https://go-numerics.github.io/docs/) |
| [**go-statistics**](https://github.com/go-statistics) | probability distributions with every method of SciPy's frozen distributions, descriptive statistics and statistical tests (`stats`) | [site](https://go-statistics.github.io/) | [docs](https://go-statistics.github.io/docs/) |
| [**go-dataframe**](https://github.com/go-dataframe) | labelled columnar tables: Series, DataFrame and Index, CSV, Arrow and Parquet, group-by, joins, reshaping, time series | [site](https://go-dataframe.github.io/) | [docs](https://go-dataframe.github.io/docs/) |
| [**go-plot**](https://github.com/go-plot) | matplotlib-class figures to SVG and PNG, with matplotlib's object model and defaults and real TeX in the labels | [site](https://go-plot.github.io/) | [docs](https://go-plot.github.io/docs/) |
| [**go-notebook**](https://github.com/go-notebook) | Go notebooks: a Markdown file whose `go` blocks are cells, compiled to a real program, ordered by what each cell uses, re-running only what changed | [site](https://go-notebook.github.io/) | [docs](https://go-notebook.github.io/docs/) |

## How the pieces fit

```
go-notebook      a notebook is a Markdown file; every library below is usable in a cell
─────────────────────────────────────────────────────────────────────────────────────
go-numerics      go-statistics      go-dataframe      go-plot
optimize,        distributions,     labelled tables   figures, SVG and PNG
integrate,       statistics,
special          tests
─────────────────────────────────────────────────────────────────────────────────────
go-ndarray       arrays, numpy.linalg, numpy.random        go-fft   numpy.fft, scipy.fft
```

go-ndarray is the bottom: the libraries above take and return its arrays, and
draw random numbers from its generator. go-ndarray's `fft` package runs on
go-fft. go-statistics takes its special functions and optimisers from
go-numerics; go-dataframe's numeric columns are go-ndarray arrays, and its
`dfplot` package draws with go-plot. A go-notebook cell can use any of them,
and shows an array, a frame or a figure through its `Display` method.

## What is measured

Each claim below is from the library's own README or documentation, which
says how it was measured.

- **Accuracy against mpmath.** go-statistics measures every distribution
  function in ULPs against [mpmath](https://mpmath.org), with SciPy measured
  on the same points: the error is at or below SciPy's in 1473 of 1479
  regions, and the six exceptions are under 4.5 ulp
  ([accuracy.md](https://github.com/go-statistics/stats/blob/main/docs/accuracy.md)).
  go-numerics' special functions: about 250 000 points in 419 regions over
  62 functions, at or below SciPy's error in 418 regions
  ([accuracy.md](https://github.com/go-numerics/special/blob/main/docs/accuracy.md)).
- **NumPy's numbers for the same seed.** go-ndarray's `random` is
  `numpy.random`: `SeedSequence`, PCG64, MT19937, Philox, SFC64 and 40
  samplers, bit-exact against NumPy 2.5.3 on x86-64, with the few ULP-level
  exceptions listed ([random.md](https://github.com/go-ndarray/ndarray/blob/main/docs/random.md)).
  On it, go-statistics' draws of 17 distributions, Dirichlet and multinomial
  are bit-identical to SciPy's, and its means, variances, 13 quantile methods
  and histograms are NumPy's bit for bit
  ([rvs-table.md](https://github.com/go-statistics/stats/blob/main/docs/rvs-table.md)).
- **The reference algorithm's path.** go-numerics ports QUADPACK, MINPACK,
  L-BFGS-B and SciPy's ODE solvers statement by statement: 116 of 124 fixture
  integrals take SciPy's exact subdivision path and 110 of 120 `solve_ivp`
  runs its exact steps ([integrate](https://github.com/go-numerics/integrate)),
  and 36 of 38 `Minimize` runs SciPy's exact path
  ([optimize](https://github.com/go-numerics/optimize)).
- **The Python reference's answers.** go-ndarray's `linalg` is checked against
  NumPy 2.5.3 on 1,040 cases; go-dataframe against pandas 3.0 on 173 recorded
  cases; go-plot's tick locations and labels on 408 axes of real figures are
  matplotlib's, every location bit for bit
  ([design.md](https://github.com/go-plot/plot/blob/main/docs/design.md)).
- **Speed, both ways.** At v0.27.0 go-fft is faster than FFTW on all 24 rows
  on Neoverse-N1 and at or above numpy.fft and scipy.fft on all 24 rows on
  every host measured ([BENCHMARKS.md](https://github.com/go-fft/fft/blob/main/BENCHMARKS.md)).
  go-ndarray against NumPy 2.5.3 and OpenBLAS: faster on reductions and
  the `*Into` forms of elementwise operations, slower on `MatMul` against 16-thread OpenBLAS
  from 512² ([perf.md](https://github.com/go-ndarray/ndarray/blob/main/docs/perf.md)).
  go-statistics against SciPy, rows where SciPy is faster included
  ([perf.md](https://github.com/go-statistics/stats/blob/main/docs/perf.md)).
  polars is faster than go-dataframe on most of the operations measured
  ([perf.md](https://github.com/go-dataframe/dataframe/blob/main/docs/perf.md)).

## Roadmap

The goal is feature parity with MATLAB (base and toolboxes) and Mathematica,
in pure Go (CGO=0), each piece validated against the reference tools of its
domain (SciPy/NumPy, mpmath, Octave, PARI/GP, Maxima, SuiteSparse, NIST StRD
and others). The plan, the home of each domain and the order of the work are
in [go-scikit/roadmap](https://github.com/go-scikit/roadmap). Five packages of the
new organisations are released (go-approx/interp v0.2.0; go-approx/poly,
go-dsp/signal, go-sparse/sparse and go-bignum/bigfloat v0.1.0).

| Wave | What | Homes |
|---|---|---|
| 1. Everyday MATLAB core, self-hosted verification | correctly rounded `big.Float` functions and a Go oracle harness; sparse matrices and iterative solvers; interpolation and splines; filter design and spectral estimation | go-bignum, go-sparse, go-approx, go-dsp |
| 2. Models and geometry | constrained and global optimisation; linear models and GLM; exact predicates, Delaunay, Voronoi; automatic differentiation; units; scientific file formats; dense linear algebra additions | go-numerics/optimize, go-statistics/models, go-geometry, go-autodiff, go-units, go-sciio, go-ndarray/linalg |
| 3. Systems, PDEs, 3D, interactivity | 3D plots and `Manipulate`; control systems; meshing and finite elements; sparse direct solvers, wavelets, Chebyshev/AAA; machine learning and time series; graph algorithms and layouts | go-plot, go-notebook, go-control, go-geometry, go-fem, go-sparse, go-dsp, go-approx, go-mlkit, go-statistics/timeseries, go-graphalgo, go-graphdrawing |
| 4. Mathematica's core | ball arithmetic, N-digit special functions and number theory; computer algebra | go-bignum, go-symbolic |

The thirteen new organisations, created on 2026-10-17:

- [**go-bignum**](https://github.com/go-bignum) ([site](https://go-bignum.github.io/), [docs](https://go-bignum.github.io/docs/)), bigfloat released v0.1.0: arbitrary precision, number theory, the Go oracle harness.
- [**go-sparse**](https://github.com/go-sparse) ([site](https://go-sparse.github.io/), [docs](https://go-sparse.github.io/docs/)), sparse v0.1.0 released: sparse matrices, orderings, iterative and direct solvers, sparse eigensolvers.
- [**go-approx**](https://github.com/go-approx) ([site](https://go-approx.github.io/), [docs](https://go-approx.github.io/docs/)), interp and poly v0.1.0 released: interpolation, splines, polynomials, Chebyshev and AAA approximation, scattered data.
- [**go-dsp**](https://github.com/go-dsp) ([site](https://go-dsp.github.io/), [docs](https://go-dsp.github.io/docs/)), signal v0.1.0 released: filter design and filtering, spectral estimation, resampling, wavelets, audio I/O.
- [**go-symbolic**](https://github.com/go-symbolic), planned: computer algebra.
- [**go-mlkit**](https://github.com/go-mlkit), planned: machine learning, clustering, classification, model selection.
- [**go-autodiff**](https://github.com/go-autodiff), planned: automatic differentiation.
- [**go-geometry**](https://github.com/go-geometry), planned: robust predicates, convex hull, Delaunay, Voronoi, k-d trees, mesh generation.
- [**go-fem**](https://github.com/go-fem), planned: finite elements and PDE solvers.
- [**go-graphalgo**](https://github.com/go-graphalgo), planned: graph algorithms.
- [**go-control**](https://github.com/go-control), planned: control systems and DAE solvers.
- [**go-units**](https://github.com/go-units), planned: units and physical constants.
- [**go-sciio**](https://github.com/go-sciio), planned: scientific file formats (HDF5, MAT, NetCDF, FITS, npy, Matrix Market, WAV/FLAC).

Read [PLAN.md](https://github.com/go-scikit/roadmap/blob/main/PLAN.md) for the
rules and the homes, [STATUS.md](https://github.com/go-scikit/roadmap/blob/main/STATUS.md)
for the state of each item, and [bib/](https://github.com/go-scikit/roadmap/tree/main/bib)
for the bibliography each item starts from.

## Repositories here

| Repo | What it is |
|------|------------|
| [**go-scikit.github.io**](https://github.com/go-scikit/go-scikit.github.io) | the portal (Hugo): the stack, one card per organisation, a first notebook |
| [**docs**](https://github.com/go-scikit/docs) | the documentation site (Hugo + Hextra), served at [/docs/](https://go-scikit.github.io/docs/) |
| [**brand**](https://github.com/go-scikit/brand) | logo, icons and social images; the mark is the stack, written by a Go program |
| [**roadmap**](https://github.com/go-scikit/roadmap) | the parity plan with MATLAB and Mathematica: homes, waves, status and bibliography |
| [**.github**](https://github.com/go-scikit/.github) | this profile and the shared Renovate preset |

The libraries themselves live in their organisations above. Everything is
BSD-3-Clause.
