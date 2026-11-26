**Force-Aware and Robustness-Aware Benchmarking of PID, Fuzzy, Fuzzy-PID, LQR and L1 Adaptive Control for Active Quarter-Car Suspensions, with an Audit of Inconsistent Simulink Logs**

Authors: Yashar Amani and Masoud Masih-Tehrani

**1. Project overview**

This study presents a unified and reproducible framework for evaluating active quarter-car suspension controllers. It compares controller performance under common plant, road, sensing-delay, and actuator conditions, while explicitly accounting for actuator effort and robustness.

The study does not propose a new suspension controller. Its main contributions are:
- An effort-normalised comparison in which each controller is compared with an LQR reference family at the same RMS actuator force. The resulting force-matched gap is reported in percentage points (pp) of ISO 2631-1 weighted RMS body acceleration.
- A controlled benchmarking protocol using a common quarter-car plant, actuator chain, sensor delay, road set, optimisation method, tuning budget, and cost function.
- A physical-consistency audit that reconstructs body acceleration from logged states and actuator force to identify Simulink records that do not satisfy the model equations.
- A controlled evaluation of L1 adaptive augmentation and a study of the effects of parameter uncertainty, sensor delay, and actuator lag.

**2. Controllers evaluated**

The benchmark evaluates five active controllers against a passive-suspension baseline:
1. PID: body-acceleration feedback with derivative filtering and anti-windup.
2. Sugeno fuzzy control: a fuzzy controller using body acceleration and sprung-mass velocity.
3. Fuzzy-scheduled PID (FPID): a PID controller with excitation-dependent scheduling of its proportional and derivative terms.
4. Output-weighted LQR: state-feedback control designed using an output-weighted quadratic cost.
5. L1 adaptive augmentation: an L1 adaptive element added to the tuned LQR baseline.
All active controllers use the same sensing and actuator models in the unified benchmark.

**3. Quarter-car model and nominal parameters**

The plant is a linear two-degree-of-freedom quarter-car model with an active force actuator acting in parallel with the passive suspension spring and damper.
Parameter	Symbol	Value
Sprung mass	m_s	400 kg
Unsprung mass	m_u	53 kg
Suspension stiffness	k_s	20,800 N/m
Suspension damping	c_s	4,800 N·s/m
Tire stiffness	k_t	80,000 N/m
Vehicle speed (random road)	v	20 m/s
Integration/control step	Δt	1 ms
Actuator force limit	F_max	±1,500 N
Actuator rate limit	F_dot_max	50,000 N/s
Actuator lag time constant	τ_a	20 ms
Nominal sensor delay	τ_d	5 ms
Stroke limit used in tuning cost	—	80 mm

The controller receives delayed state measurements and delayed applied actuator force. The actuator includes saturation, a rate limiter, and first-order lag.

**4. Road excitations**

The six test roads described in the manuscript are:
- 20 mm step.
- Staircase road varying between −12 mm and +20 mm.
- Variable-frequency sweep with a peak amplitude of approximately 12 mm, passing through the body resonance.
- 10 mm sinusoidal road.
- Single bump with a height of 20 mm.
- Random ISO 8608 class-C road profile.
For the random class-C test road, the roughness coefficient is G_d(n_0) = 256 × 10⁻⁶ m³ at n_0 = 0.1 cycle/m. The profile is generated spectrally for a vehicle speed of 20 m/s and a duration of 10 s. The test-road random seed is 21. The tuning roads use class B (seed 11) and class D (seed 12), keeping the tuning roads separate from the class-C test road.

**5. Evaluation methodology**

Comfort
The primary comfort measure is ISO 2631-1 W_k frequency-weighted RMS body acceleration. The frequency weighting is applied in the frequency domain and is validated against the standard's tabulated W_k values to within 0.2%.

The manuscript also reports vibration dose value (VDV) and maximum transient vibration value (MTVV) in the supplementary results. The weighted acceleration is calculated from body acceleration, not seat acceleration; it is therefore used as a comfort proxy.
Road holding, suspension travel, and actuator effort
The evaluation includes:
- RMS tire deflection.
- RMS and peak suspension stroke.
- Dynamic-to-static tire-load ratio.
- RMS and peak actuator force.
- Actuator saturation percentage.
- Positive actuator work.
Effort-normalised comparison
For each controller, the reduction in weighted RMS acceleration relative to the passive suspension is compared with the best result from a family of 45 LQR designs at the same RMS actuator force.

The force-matched gap, Δ_F, is expressed in percentage points:
- Δ_F > 0: the controller achieves better comfort than the LQR reference family at the same RMS force.
- Δ_F ≈ 0: its comfort result is explained by actuator effort, within the comparison tolerance.
- Δ_F < 0: the controller performs below the force-matched LQR reference.
Tuning and robustness
Tunable controllers use the same Nelder–Mead optimiser, 60-function-evaluation budget, initial parameter vector, and tuning cost. Tuning scenarios use class-B and class-D roads and perturbed plant parameters; the class-C test road is kept separate.
The robustness study includes parameter variations, damper fade, increased sensor delay, increased actuator lag, and a 30-sample Monte Carlo study.

**6. Main findings reported in the manuscript**

The following are the principal findings from the manuscript's simulations:

- Across the six test roads, the active controllers reduce ISO 2631-1 weighted RMS body acceleration by approximately 8.4–50.5% relative to the passive suspension.
- On the class-C random road, reductions range from 10.2% to 14.2%. Fuzzy and FPID control rank ahead of LQR in the unadjusted comfort comparison, but use approximately 27–30% more RMS actuator force and 15–19% more suspension stroke than LQR.
- After comparison with an LQR family at matched RMS force, the fuzzy-type advantage on the class-C random road falls to within approximately ±1 percentage point. A larger effort-independent advantage remains on the frequency sweep through body resonance (about 4 pp).
- The tuned L1 adaptive controller is effectively equivalent to its LQR baseline in the reported comparison (paired difference of approximately 0.01 pp). The tuned projection bound reaches its lower clamp; larger bounds degrade comfort in the reported ablation.
- A 20 ms sensor delay removes approximately 39–57% of the achievable comfort gain. A 40 ms actuator lag removes approximately 34–46%. These effects are larger than those observed for the tested ±20% parameter variations.
- The physical-consistency audit rejects all 20 legacy Simulink records examined. The manuscript reports that their logged forces are only about 0.3–1.7% of the force implied by the reconstructed acceleration.
These findings are simulation results and should be interpreted within the model scope and limitations described below.

**7. Repository contents**

The manuscript identifies the following implementation files and outputs as supplementary material. Once uploaded, organise the repository so that reviewers can locate them easily. Update this section if the final repository uses different paths or filenames.
.
├── README.md
├── Manuscript_Full_v7.md
├── Code/
│   ├── suspension_q1_v2_unified.m
│   ├── mirror.py
│   ├── supp.py
│   ├── supp2.py
│   ├── supp3.py
│   ├── supp4.py
│   └── supp5.py
├── Results/
│   └── [generated CSV tables]
└── Figures/
    └── [generated vector-PDF figures]
The MATLAB script produces the main benchmark results, CSV tables, and vector-PDF figures. The independent Python implementation is used for the force-matched analysis, cross-implementation checks, and L1 ablation analyses.
Important: The legacy Simulink logs are not part of the public result ranking in the manuscript. They were audited and found inconsistent with the stated plant equations. The manuscript states that these logs are available from the corresponding author.

**8. Software requirements and reproduction**

MATLAB implementation
The main benchmark was produced using:
- MATLAB R2018b or later.
- Control System Toolbox.
To run the main MATLAB benchmark, place the MATLAB script on the MATLAB path and execute it:
run('suspension_q1_v2_unified.m')
The script is described in the manuscript as writing the generated tables to CSV and figures as vector PDF files.
Python implementation
The independent Python re-implementation and supplementary analyses use:
- Python.
- NumPy.
- SciPy.
The manuscript identifies mirror.py and supp.py through supp5.py as the Python implementation and analysis scripts. Run each script from the project directory using the Python interpreter, following any configuration or execution notes included in the script.
The manuscript reports agreement between the MATLAB and Python implementations within 0.1 pp on deterministic roads and within approximately 0.2–1.0 pp on random-road results. The two implementations use different random-number generators, so their random-road realisations are not identical.
Exact package versions are not specified in the manuscript. A pinned requirements.txt should be added if exact software-version reproduction is required.

**9. Scope and limitations**

The results should be interpreted in light of the following limitations stated in the manuscript:
- The plant is a linear quarter-car model.
- Measurements are ideal, full-state, and noise-free; measurement noise and quantisation are not modelled.
- Nonlinear damping, tire lift-off, and pitch–bounce coupling are not included.
- The comfort weighting is applied to body acceleration rather than seat acceleration.
- The results are based on simulation; no hardware-in-the-loop or test-rig validation is reported.
- The force-matched LQR reference uses a finite 45-design grid and interpolation. The manuscript identifies this as a limitation, particularly for the modest sweep and bump gaps.
- The MATLAB and Python implementations use different random-number generators, which produce different random-road realisations.
Accordingly, the results do not establish that the controller ranking will transfer directly to a physical vehicle.

**10. Data and code availability**

The manuscript states that the MATLAB script (suspension_q1_v2_unified.m), the Python re-implementation and analysis scripts (mirror.py, supp.py, and supp2.py–`supp5.py`), and generated CSV tables are provided as supplementary material.

The legacy Simulink logs are available from the corresponding author. Do not assume that these logs are included in this repository unless they have been explicitly added.

## 11. Associated Manuscript

This repository contains the source code, simulation datasets, and supplementary results associated with a research manuscript currently submitted for peer review.

Full bibliographic information will be added following publication.

## 12.License

No open-source license has been applied to this repository at this time.
## 13. Archived Version and DOI

An archived version of this research project is available through Zenodo.

**GitHub Repository:** https://github.com/Yashark2/Suspension_Q1_V2_Unified

**Zenodo DOI:** https://doi.org/10.5281/zenodo.24559565

The archived version corresponds to the source code and supplementary materials used to generate the results reported in the associated research manuscript.

The source code, simulation datasets, figures, and documentation are made publicly available for research inspection and peer-review purposes.

All rights remain with their respective rights holders. Permission should be obtained from the appropriate rights holders before reusing, modifying, or redistributing these materials.

For permission requests, please contact the corresponding author.
