# In-Silico Metabolic Engineering of *E. coli* for Naringenin Production

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)
![CobraPy](https://img.shields.io/badge/Library-CobraPy-green)
![Model](https://img.shields.io/badge/Base_Model-iJO1366-orange)

Constraint-based metabolic modeling and yield optimization for the heterologous production of the plant flavonoid **Naringenin** in *Escherichia coli* using **Genome-Scale Metabolic Models (GSMMs)** and **Flux Balance Analysis (FBA)**.

---

## Executive Summary

Microbial production of complex plant secondary metabolites provides a sustainable alternative to chemical synthesis and agricultural extraction. However, natural chassis organisms like *E. coli* lack native pathways for flavonoid biosynthesis and naturally divert carbon flux toward biomass accumulation rather than secondary metabolites.

In this project, I integrated a 4-step heterologous plant pathway into the genome-scale metabolic reconstruction of *E. coli* (`iJO1366`). Using **CobraPy**, I modeled pathway thermodynamics, identified endogenous precursor bottlenecks (L-Tyrosine and Malonyl-CoA), and simulated single-gene knockouts ($\Delta pgi$) to establish a growth-coupled production phenotype.

---

## 🧬 Biological Pathway Architecture

Naringenin synthesis is engineered by extending the endogenous *E. coli* aromatic amino acid pathway (specifically L-Tyrosine) through four heterologous enzymes:

1. **TAL** (*Tyrosine Ammonia-Lyase*): Deaminates L-Tyrosine to p-Coumaric acid.
2. **4CL** (*4-Coumarate-CoA Ligase*): Activates p-Coumaric acid into 4-Coumaroyl-CoA.
3. **CHS** (*Chalcone Synthase*): Condenses 1 molecule of 4-Coumaroyl-CoA with 3 molecules of Malonyl-CoA to yield Naringenin Chalcone.
4. **CHI** (*Chalcone Isomerase*): Catalyzes the stereospecific isomerization of Naringenin Chalcone into Naringenin.

<pre>
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
</pre>

---

## 📋 Reaction Specifications

| Reaction ID | Name | Equation |
| :--- | :--- | :--- |
| **TAL** | Tyrosine Ammonia-Lyase | `tyr_L → p_coumarate + nh4` |
| **4CL** | 4-Coumarate-CoA Ligase | `p_coumarate + atp + coa → coumaroyl_coa + amp + ppi` |
| **CHS** | Chalcone Synthase | `coumaroyl_coa + 3 malcoa → naringenin_chalcone + 4 coa + 3 co2` |
| **CHI** | Chalcone Isomerase | `naringenin_chalcone → naringenin` |
| **DM_naringenin** | Demand Reaction | `naringenin → ∅` |

---

## 🧮 Mathematical Formulation & FBA Strategy

Flux Balance Analysis assumes a quasi-steady state ($S \cdot v = 0$) where $S$ is the $m \times n$ stoichiometric matrix and $v$ is the vector of metabolic fluxes:

$$\max_{v} \quad z = c^T v$$

$$\text{subject to} \quad S \cdot v = 0$$

$$lb_i \le v_i \le ub_i \quad \forall i \in \{1, \dots, n\}$$

### Objective Functions Evaluated:
1. **Wild-Type / Baseline Growth:** Objective vector $c$ maximizes the biological biomass reaction (`BIOMASS_iJO1366_WT_53RT`).
2. **Maximal Production Potential:** Objective vector $c$ maximizes the demand reaction `DM_naringenin`.
3. **Growth Coupling Evaluation:** Scanning boundary conditions across biomass production levels to plot Production Envelope graphs.

---

## 📁 Repository Structure

<pre>
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
</pre>

---

## 🔬 Computational Workflow & Notebooks

### `01_Model_Setup.ipynb`
* Loads base *E. coli* GSMM `iJO1366` from BiGG database.
* Defines metabolites, exchange bounds, and heterologous reactions with full chemical formulas and charge balance.
* Exports modified model to JSON/SBML format under `data/`.

### `02_Baseline_FBA.ipynb`
* Performs baseline Flux Balance Analysis under aerobic glucose conditions ($10\text{ mmol/gDW/h}$).
* Evaluates theoretical maximum Naringenin yield vs. growth rate trade-offs.
* Identifies precursor drain bottlenecks: **Malonyl-CoA** (fatty acid biosynthesis pathway competition) and **L-Tyrosine** (aromatic pathway flux).

### `03_Gene_Knockout_Optimization.ipynb`
* Runs a systematic single-gene deletion screen across non-essential metabolic genes.
* Identifies metabolic knockouts such as **$\Delta pgi$** (*Glucose-6-phosphate isomerase*):
  * Redirects glucose flux into the **Pentose Phosphate Pathway (PPP)**.
  * Increases NADPH generation, supporting elevated precursor synthesis.
  * Forces growth-coupled production where cell growth requires flux through the engineered secondary metabolite pathway.

---

## 💻 How to Run

### 1. Clone the Repository
```bash
git clone https://github.com/rraman-gpt/metabolic-engineering-ecoli.git
cd metabolic-engineering-ecoli
2. Create and Activate a Virtual Environment
PowerShell
# On Windows PowerShell
python -m venv venv
.\venv\Scripts\activate

# On macOS/Linux
python3 -m venv venv
source venv/bin/activate
3. Install Dependencies
Bash
pip install -r requirements.txt
4. Launch Jupyter Notebooks
Bash
jupyter notebook
Navigate to notebooks/01_Model_Setup.ipynb to execute the pipeline sequentially.

🛠️ Tech Stack
Language: Python 3.9+

Modeling Framework: CobraPy

Solver: GLPK (GNU Linear Programming Kit) / scipy.optimize

Data Handling & Analytics: Pandas, NumPy

Visualization: Matplotlib

👤 Author
Raman Gupta

Dual Degree Candidate @ BITS Pilani (Pilani Campus)

🎓 M.Sc. (Hons) Biological Sciences + B.E. Electrical & Electronics Engineering (EEE)

Email: rramangpt@gmail.com

LinkedIn: https://www.linkedin.com/in/raman-gupta-56b75138a/

GitHub: https://github.com/rraman-gpt