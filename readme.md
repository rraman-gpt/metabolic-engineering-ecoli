# In-Silico Metabolic Engineering of *E. coli* for Naringenin Production

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)
![CobraPy](https://img.shields.io/badge/Library-CobraPy-green)
![Model](https://img.shields.io/badge/Base_Model-iJO1366-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

Constraint-based metabolic modeling and yield optimization for the heterologous production of the plant flavonoid **Naringenin** in *Escherichia coli* using **Genome-Scale Metabolic Models (GSMMs)** and **Flux Balance Analysis (FBA)**.

---

## Executive Summary

Microbial production of complex plant secondary metabolites provides a sustainable alternative to chemical synthesis and agricultural extraction. However, natural chassis organisms like *E. coli* lack native pathways for flavonoid biosynthesis and naturally divert carbon flux toward biomass accumulation rather than secondary metabolites.

This project integrates a 4-step heterologous plant pathway into the genome-scale metabolic reconstruction of *E. coli* (`iJO1366`). Using **CobraPy**, we model pathway thermodynamics, identify endogenous precursor bottlenecks (L-Tyrosine and Malonyl-CoA), and simulate single-gene knockouts ($\Delta pgi$) to establish a growth-coupled production phenotype.

---

## 🧬 Biological Pathway Architecture

Naringenin synthesis is engineered by extending the endogenous *E. coli* aromatic amino acid pathway (specifically L-Tyrosine) through four heterologous enzymes:

1. **TAL** (*Tyrosine Ammonia-Lyase*): Deaminates L-Tyrosine to $p$-Coumaric acid.
2. **4CL** (*4-Coumarate-CoA Ligase*): Activates $p$-Coumaric acid into $4$-Coumaroyl-CoA.
3. **CHS** (*Chalcone Synthase*): Condenses 1 molecule of $4$-Coumaroyl-CoA with 3 molecules of Malonyl-CoA to yield Naringenin Chalcone.
4. **CHI** (*Chalcone Isomerase*): Catalyzes the stereospecific isomerization of Naringenin Chalcone into Naringenin.

   [ Endogenous Metabolism ]
               │
          L-Tyrosine
               │ (TAL)
        p-Coumaric Acid
               │ (4CL) + CoA
       4-Coumaroyl-CoA  +  3 Malonyl-CoA
               │
               ├──► (CHS)
        Naringenin Chalcone
               │ (CHI)
          ✨ NARINGENIN ✨
               │
        (Demand / Export)

### Reaction Specifications

| Reaction ID | Name | Equation |
| :--- | :--- | :--- |
| `TAL` | Tyrosine Ammonia-Lyase | $\text{tyr\_\_L} \longrightarrow \text{p\_coumarate} + \text{nh4}$ |
| `4CL` | 4-Coumarate-CoA Ligase | $\text{p\_coumarate} + \text{atp} + \text{coa} \longrightarrow \text{coumaroyl\_coa} + \text{amp} + \text{ppi}$ |
| `CHS` | Chalcone Synthase | $\text{coumaroyl\_coa} + 3\,\text{malcoa} \longrightarrow \text{naringenin\_chalcone} + 4\,\text{coa} + 3\,\text{co2}$ |
| `CHI` | Chalcone Isomerase | $\text{naringenin\_chalcone} \longrightarrow \text{naringenin}$ |
| `DM_naringenin`| Demand Reaction | $\text{naringenin} \longrightarrow \varnothing$ |

---

## 🧮 Mathematical Formulation & FBA Strategy

Flux Balance Analysis assumes a quasi-steady state ($\frac{dX}{dt} = S \cdot v = 0$) where $S$ is the $m \times n$ stoichiometric matrix and $v$ is the vector of metabolic fluxes:

$$\max_{v} \quad z = c^T v$$

$$\text{subject to} \quad S \cdot v = 0$$

$$lb_i \le v_i \le ub_i \quad \forall i \in \{1, \dots, n\}$$

### Objective Functions Evaluated:
1. **Wild-Type / Baseline Growth:** Objective vector $c$ maximizes the biological biomass reaction (`BIOMASS_iJO1366_WT_53RT`).
2. **Maximal Production Potential:** Objective vector $c$ maximizes the demand reaction `DM_naringenin`.
3. **Growth Coupling Evaluation:** Scanning boundary conditions across biomass production levels to plot Production Envelope graphs.

---

## 📁 Repository Structure

```text
metabolic-engineering-ecoli/
├── data/
│   └── iJO1366_naringenin.json       # Modified GSMM containing heterologous reactions
├── notebooks/
│   ├── 01_Model_Setup.ipynb          # Model loading, pathway addition & SBML validation
│   ├── 02_Baseline_FBA.ipynb         # Wild-type FBA, yield trade-offs & flux distribution
│   └── 03_Gene_Knockout_Optimization.ipynb # In-silico gene deletion screen (e.g., pgi)
├── .gitignore                        # Python/Jupyter tracking exemptions
├── readme.md                         # Project documentation
└── requirements.txt                  # Environment dependencies
🔬 Computational Workflow & Notebooks01_Model_Setup.ipynbLoads base E. coli GSMM iJO1366 from BiGG database.Defines metabolites, exchange bounds, and heterologous reactions with full chemical formulas and charge balance.Exports modified model to JSON/SBML format under data/.02_Baseline_FBA.ipynbPerforms baseline Flux Balance Analysis under aerobic glucose conditions ($10\text{ mmol/gDW/h}$).Evaluates theoretical maximum Naringenin yield vs. growth rate trade-offs.Highlights precursor drain bottlenecks: Malonyl-CoA (fatty acid biosynthesis pathway competition) and L-Tyrosine (aromatic pathway flux).03_Gene_Knockout_Optimization.ipynbRuns a systematic single-gene deletion screen across non-essential metabolic genes.Identifies metabolic knockouts such as $\Delta pgi$ (Glucose-6-phosphate isomerase):Redirects glucose flux into the Pentose Phosphate Pathway (PPP).Increases NADPH generation, supporting elevated precursor synthesis.Forces growth-coupled production where cell growth requires flux through the engineered secondary metabolite pathway.💻 Installation & Quickstart1. Clone the RepositoryBashgit clone [https://github.com/rraman-gpt/metabolic-engineering-ecoli.git](https://github.com/rraman-gpt/metabolic-engineering-ecoli.git)
cd metabolic-engineering-ecoli
2. Create and Activate a Virtual EnvironmentBash# On Windows
python -m venv venv
.\venv\Scripts\activate

# On macOS/Linux
python3 -m venv venv
source venv/bin/activate
3. Install DependenciesBashpip install -r requirements.txt
4. Launch Jupyter NotebooksBashjupyter notebook
Navigate to notebooks/01_Model_Setup.ipynb to execute the pipeline sequentially.🛠️ Tech StackLanguage: Python 3.9+Modeling Framework: CobraPySolver: GLPK (GNU Linear Programming Kit) / scipy.optimizeData Handling & Analytics: Pandas, NumPyVisualization: Matplotlib👤 AuthorRaman GuptaDual Degree Candidate @ BITS Pilani (Pilani Campus)🎓 M.Sc. (Hons) Biological Sciences + B.E. Electrical & Electronics Engineering (EEE)Email: rramangpt@gmail.comLinkedIn: raman-gupta-56b75138aGitHub: @rraman-gpt