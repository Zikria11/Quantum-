# Syndrome Resampling in Quantum Error Correction Reproduction, collision statistics, finite-sample certification, and exact-population validation

This repository contains an exploratory research notebook, [`Untitled19.ipynb`](./Untitled19.ipynb), investigating syndrome resampling (SR) for quantum error correction (**QEC**). It starts with rotated surface-code memory simulations under circuit-level noise, studies the statistical problems created by rare or repeated syndrome strings, and then reproduces and extends numerical checks of the syndrome-resampling method using the original authors' code-capacity simulation model.

The emphasis of the later experiments is what can actually be estimated and certified from finite syndrome data, not simply whether a resampled point estimate is smaller than the unconditioned logical error rate. Research status: Experimental and diagnostic. The notebook records pilots, archived-data analyses, frozen replications, and mathematical self-tests. It does not establish a new threshold theorem, a universal finite-sample guarantee, or a new decoder. The early circuit-level experiments and later code-capacity experiments use different noise models and should not be compared as if they were the same benchmark.

Research questions
- How does the probability of obtaining repeated syndromes scale with code distance, sample size, and resampling power?
- When do finite-data syndrome-resampling estimates become sparse, unstable, or dominated by one syndrome?
- Can independent calibration and evaluation data support meaningful upper confidence bounds on resampled logical failure probabilities?
- How do groupwise bounds, direct collision-based Clopper–Pearson bounds, and conservative all-pairs bounds compare at fixed shot budgets?
- Do empirical upper bounds cover exact population targets when those targets can be computed independently?
- Where do pair and triple collision methods become statistically uninformative?
Mathematical setup
Let $S$ denote a measured detector-syndrome string, $p_s = \Pr(S=s)$, and
$f_s = \Pr(\text{logical decoding failure}\mid S=s)$.

For integer resampling power $\alpha\geq 1$, the syndrome-powered distribution and its logical failure target are
\[
q_\alpha(s)=\frac{p_s^\alpha}{C_\alpha},\qquad
C_\alpha=\sum_s p_s^\alpha,\qquad
R_\alpha=\frac{\sum_s p_s^\alpha f_s}{\sum_s p_s^\alpha}.
\]
Here, $R_1=\sum_s p_s f_s$ is the ordinary logical error rate (**LER**). The notebook also examines a dominant-excluded target, which removes one syndrome $s_0$ selected from an independent calibration sample:
\[
R_\alpha^{(-s_0)}
=\frac{\sum_{s\neq s_0}p_s^\alpha f_s}
       {\sum_{s\neq s_0}p_s^\alpha}.
\]
The distinction matters: removing a syndrome changes the estimand. A low resampled **LER** is not automatically a certified bound, a physical reduction in the underlying noise, or evidence of a higher fault-tolerance threshold.
Estimation and certification approaches
## Finite-data falling-factorial resampling: uses syndrome multiplicities and $(n_s)_\alpha$ weights to estimate a powered distribution.
## Collision diagnostics: counts matched syndrome pairs/triples and tracks support size, collision moments, dominant records, and finite-sample failures.
## Held-out and groupwise analysis: fixes choices from calibration data, then evaluates conditional failures on independent samples using grouped concentration bounds.
## Disjoint-tuple Clopper–Pearson (CP) bounds: divides independent evaluation shots into disjoint pairs ($\alpha=2$) or triples ($\alpha=3$), retaining tuples whose syndromes match. Conditional on the number of matching tuples, failures supply a binomial experiment for the corresponding powered-syndrome target.
## Conservative all-pairs comparisons: investigates U-statistic estimators with Hoeffding-style concentration, including cases where the resulting bound is vacuous.
## Exact population calculations: computes the joint distribution of syndromes and logical observable outcomes for small code-capacity circuits, then checks finite-sample procedures against exact $R_\alpha$ values.
Confidence-level note: Reported 95% procedures are marginal, for a specified method, target, and replicate. They should not be interpreted as simultaneous 95% guarantees across all methods, groups, configurations, or selected plots.
Experimental models
Track	Circuit / noise model	Typical settings	Purpose
Initial surface-code study	Stim surface_code:rotated_memory_x; circuit-level depolarizing, reset, and measurement noise; PyMatching **MWPM**	$d=3,5,7$ (some exploratory cells use $d=9$); often $p=0.**005**$, rounds $=d$	Baseline **LER**, syndrome sparsity, collisions, pilot bounds and ablations
Authors' reproduction and certification	Original archived surface_code_capacity_zero construction; code-capacity bit-flip noise; PyMatching **MWPM**	Mostly $d=5,7,9$, $p=0.12,0.14$, $\alpha=2$; additional $d=3$ and $\alpha=3$ tests	Paper-method pilots, matched-model replications, exact truth, pair/triple certificates

The parameter $p$ and resulting logical error rates are not directly comparable across these two tracks. Their physical noise channels, circuits, and detector spaces differ.
Notebook guide
The notebook contains 84 code cells and no Markdown section cells. The ranges below use zero-based code-cell indices to help locate experiments; they represent research stages rather than a single clean, restartable pipeline.
Cell indices	Content
0–16	Dependencies; rotated surface-code construction; baseline **MWPM**; Wilson confidence intervals; circuit and detector-error-model inspection
17–29	Synthetic powered-distribution checks; total variation; detector sparsity; pair/triple collisions; held-out SR; dominant-syndrome diagnostics
30–45	Larger circuit-level validation runs; batch and coverage diagnostics; candidate certificates; overlap/variance checks; direct and groupwise pilot bounds
46–59	Independent calibration/evaluation archives; frozen grouping; noise generalization; dominant-syndrome ablations; independent audits and concentration variants
60–71	Export helper; cross-distance diagnostics; benchmark comparisons; calibration alignment; synthetic repeated-sampling coverage and statistical-power tests
72–75	Fetch and inspect the original authors' Zenodo archive; run an initial code-capacity reproduction
76–79	Further reproduction, independent-batch Figure 4 pilot, matched certification, and direct collision-bound comparison
80	Frozen 20-seed, six-configuration matched-model replication with groupwise, direct-pair CP, and all-pairs comparisons
81	Exact code-capacity population truth and analytic direct-pair CP coverage experiments
82	Retrospective exact-truth audit of frozen $d=5$ certificates, without new Stim sampling
83	Archived-data $\alpha=3$ direct-disjoint-triple crossover against the original prospective results

Selected recorded findings
These numbers are outputs saved in the notebook, not fresh benchmark runs. Monte Carlo estimates vary across seeds, batches, and experimental stages.
## Syndrome collisions become scarce at larger circuit-level codes
At $p=0.**005**$ and **100**,**000** circuit-level shots:
Code distance	Unique syndrome strings	Matching unordered pairs	Matching unordered triples
3	8,**215**	**914**,**868**,**136**	12,**831**,**028**,**287**,**860**
5	92,**566**	1,**064**,**511**	**484**,**628**,**580**
7	**100**,**000**	0	0

These are combinatorial tuple counts, not independent matched-pair or matched-triple sample sizes. A separate $d=7$ scaling run found only 1,**483** matching unordered pairs among 10 million shots, with strong concentration of the $\alpha=3$ weight on a single syndrome. This motivates checking effective support and the number of disjoint matched tuples before interpreting resampled estimates.
## The original code-capacity SR pilot is reproducible
Using the authors' archived circuit and resampling implementation, with $d=5$, $\alpha=2$, and **100**,**000** shots per physical error rate:
Physical error $p$	Baseline **LER**	Empirical SR **LER**
0.12	0.**17700**	0.**037475**
0.14	0.**23257**	0.**084675**

This is an implementation pilot, not an independently established threshold improvement. The subsequent experiments focus on the uncertainty and reliability of such estimates.
## Frozen direct-pair certificates are more informative in the tested $d=5$ regimes
In the frozen code-capacity replication (20 independent seeds per configuration, 50,**000** calibration shots and **100**,**000** evaluation shots per replicate), the full $\alpha=2$ target gave:
Configuration	Median groupwise upper bound	Median disjoint-pair CP upper bound
$d=5, p=0.12$	0.**361036**	0.**064692**
$d=5, p=0.14$	0.**474892**	0.**115461**

At $d=7$ the groupwise procedure was vacuous in these configurations, and direct pairs were frequently sparse or absent. At $d=9$, both were vacuous in the recorded experiment. The comparison is empirical and limited to the frozen protocols and sampled parameter settings.
## Exact truth allows a separate coverage check
For the authors' code-capacity model, exact population calculations gave:
Configuration	Exact baseline **LER**	Exact $R_2$	Exact $R_3$
$d=5, p=0.12$	0.**177317**	0.**038279**	0.**006540**
$d=5, p=0.14$	0.**231386**	0.**081536**	0.**019160**

The retrospective 20-seed audit counted direct-pair CP coverage of 20/20 for the full $d=5,p=0.12$ target and 18/20 for the full $d=5,p=0.14$ target. These small sample counts do not accurately determine long-run coverage. The separate 5,**000**-replicate analytic direct-pair experiment is recorded in cell 81.
## Alpha-three certificates can fail to be informative
In the matched archived-data crossover using 1 million calibration and 2 million evaluation shots, $\alpha=3$ disjoint triples yielded:
- Full target: one matching triple and CP upper bound 0.95.
- Calibration-dominant-excluded target: zero matching triples and CP upper bound 1.0.
These bounds are valid but effectively uninformative in the tested regime. A low powered-syndrome point estimate by itself does not resolve the sample-complexity problem.
Getting started
The intended environment is Google Colab with Python 3. The notebook also uses Colab-specific download helpers in one export cell.
python -m pip install stim pymatching numpy scipy pandas matplotlib requests
Versions printed in the stored notebook run include Stim 1.16.0, PyMatching 2.4.0, and NumPy 2.4.6. Exact package versions are not locked; differences can affect simulation or compatibility. The notebook includes a targeted NumPy 2 compatibility shim for the original authors' code.

Suggested way to explore:
## Open Untitled19.ipynb in Colab and run the initial installation, imports, and small baseline/synthetic tests (cells 0–21). Reduce multi-million-shot budgets as needed.
## For the paper reproduction, run archive retrieval/extraction (cells 72–73), then the archived-code pilot (cell 75). This requires Internet access to retrieve the official archive.
## Run the later reproduction and certification cells in their intended order, retaining the generated folders. Many later cells read NPZ/CSV/pickle outputs from earlier experiments; they are not standalone without those artifacts.
## Review the frozen settings and target definitions before modifying seeds, shot budgets, calibration rules, or test configurations. If settings change, treat the results as a new experiment rather than the same frozen replication.
Do not select Runtime > Run all expecting a quick, reproducible pipeline. The notebook accumulates multiple research stages, redefines some functions/variables, contains long simulations, and includes an archive/export cell. Later analyses may fail if historical outputs have not been generated or restored. The notebook file alone does not include every data archive referenced by its code.
Outputs and data provenance
The notebook writes files relative to the current working directory (normally /content in Colab). Examples include:
baseline_circuit_noise_p005.csv
experiment_config.json
synthetic_resampling_convergence.csv
syndrome_sparsity_experiment.csv
collision_statistics.csv
qec_resampling_validation/
qec_d5_alpha2_validation/
qec_d5_ablated_prospective_v1/
original_paper_reproduction/
    zenodo_manifest.json
    authors_code/
    figure4_pilot/
    figure4_independent_v1/
    matched_certification_v1/
    direct_collision_baselines_v1/
    matched_replication_frozen_v1/
    all_replicates.csv
    replication_summary.csv
    counts/
    exact_population_v1/
    exact_truth.csv
    exact_truth_coverage_audit_v1/
qec_alpha3_crossover_v1/
This tree is illustrative, not a claim that every listed artifact is already bundled with the notebook. When publishing or rerunning the study, preserve the configuration **JSON**, saved counts, seed information, independent evaluation files, and audit outputs needed to verify each figure or claim.
Reproducibility cautions
- Two distinct physical noise models: do not pool or directly compare their error-rate curves.
- Calibration vs evaluation: independently select data-dependent exclusions or groups in calibration, then estimate/certify on held-out evaluation.
- Different targets: baseline **LER**, full $R_\alpha$, and dominant-excluded $R_\alpha^{(-s_0)}$ answer different questions.
- Missing matches: zero matched tuples does not prove zero logical errors. A bound of 1 is typically the honest result.
- Exact conditional failures: even with a deterministic decoder, $f_s$ need not be 0 or 1 because the same syndrome can be associated with multiple logical outcomes.
- Reused notebook state: some earlier code and stored tables, including a manually constructed baseline **CSV** table, come from separate runs. Use the exact provenance and seeds for each reported number.
- Selective inference: 95% one-sided marginal bounds are not automatically simultaneous, and 20-replicate coverage summaries have substantial sampling uncertainty.
- Interpretation: simulation results are not hardware validation, a logical-distance certification, or a proof of improved fault-tolerance threshold.
Original work and attribution
This notebook builds on the syndrome-resampling work by Luis Colmenarez, Aron Marton, and Markus Mueller (**2026**), described in the official code archive as “Syndrome resampling enhances quantum error correction thresholds.”
- Official authors' Zenodo record and code archive
- The notebook downloads syndrome_resampling.zip from that record and uses sampler_square.py and surface_code_simulation/utils_surface_code.py for the matched reproduction.
The notebook's additional experiments, finite-sample comparisons, and audit code should be distinguished from the original authors' implementation. Consult the archive's own **LICENSE** for the conditions governing reuse of the original code.
Scope
This is a research notebook and reproducibility record, not a packaged Python library or a fully automated benchmark suite. Its strongest current contribution is a detailed experimental examination of when syndrome-resampling estimates and statistical confidence certificates remain informative, and when collision scarcity makes them vacuous.
