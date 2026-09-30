# In-Silico Metabolic Engineering of E. coli for Naringenin Production

## Executive Summary
This project utilizes constraint based Genome-Scale Metabolic Modeling (GSMM) to engineer an *E. coli* strain for the heterologous production of Naringenin, a high-value plant flavonoid (a secondary metabolite). Using **CobraPy** and Flux Balance Analysis (FBA), plant-specific enzymatic pathways were integrated into the `iJO1366` *E. coli* model, and genetic knockouts were simulated to optimize carbon flux toward the secondary metabolite.

## 🧬 The Biological Challenge
Producing complex plant metabolites in microbial chassis often suffers from low yield because the host's natural metabolism diverts carbon flux toward biomass (growth) rather than the introduced pathway. This project identifies genetic intervention strategies to couple product yield with cellular growth.

## 🛠 Methodology
1. **Base Model:** E. coli iJO1366 (Genome-scale model).
2. **Heterologous Pathway:** Introduction of TAL, 4CL, CHS, and CHI enzymes to convert endogenous L-Tyrosine into Naringenin.
3. **Simulation:** Flux Balance Analysis (FBA) and Single Gene Deletion screening using CobraPy.

## 💻 How to Run