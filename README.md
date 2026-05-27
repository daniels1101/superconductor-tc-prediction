# Superconductor Tc Prediction

Machine learning pipeline to predict the critical temperature $T_c$ of superconductors from elemental features. The trained model is then applied to a proposed A15-structured vanadium phase and used to investigate how elemental substitution affects the predicted $T_c$.

---

## Results

| Model | RMSE (K) | R² |
|---|---|---|
| Linear Regression | 17.38 | 0.738 |
| Gradient Boosting | 12.34 | 0.868 |
| Random Forest | 9.03 | 0.929 |

| System | Predicted Tc | Source |
|---|---|---|
| BCC V (reference) | 5.4 K | Experimental literature |
| Pure A15 V | 4.36 K | This model |
| V₀.₉₈Zr₀.₀₂ A15 | 8.19 K | This model |
| Experimental observation | ~15 K | Magnetization measurement |

The model correctly captures the direction of the trend. The gap between 8.19 K and 15 K is the structural Tc enhancement from the A15 crystal geometry, an effect no elemental model can recover.

---

## Dataset

Hamidieh (2018) superconductor dataset from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/464/superconductivty+data) (ID 464).

- 21,263 superconductors
- 81 features derived from elemental properties
- Target variable: critical temperature in Kelvin
- Fetched directly via `ucimlrepo` no manual download required

---

## Notebook structure

| Section | Contents |
|---|---|
| 1. Imports | Libraries and dependencies |
| 2. Data loading | Fetch from UCI, load into SQLite |
| 3. SQL exploration | Tc distribution, 77 K threshold analysis |
| 4. Data preparation | Train/test split |
| 5. Modeling | Linear Regression, Random Forest, Gradient Boosting |
| 6. Results | Predicted vs actual Tc plots |
| 7. Feature importance | Top 10 features, physics interpretation |
| 8. Real-world application | A15 V phase prediction and analysis |
| 9. Inverse analysis | Feature sensitivity toward observed Tc |
| 10. Composition optimization | V₁₋ₓZrₓ and V₁₋ₓOₓ composition scans |
| 11. Summary | Full results table and conclusions |

---

## Key findings

**Thermal conductivity dominates.** The top two features account for over 66% of predictive power. This is physically meaningful: in BCS theory, superconductivity arises from electron-phonon coupling, and thermal conductivity reflects how phonons propagate through the lattice.

**Elemental models cannot distinguish polymorphs.** Pure A15 V and BCC V have identical feature vectors. The Tc enhancement from 5.4 K to ~15 K in the A15 structure is driven by crystal geometry, enhanced electron density of states at the Fermi level and stronger electron-phonon coupling, none of which are captured by elemental averages.

**Zr substitution raises the prediction but hits a ceiling.** Introducing 2 at% Zr activates variance features that are zero for pure V, pushing the prediction from 4.36 K to 8.19 K. Adding more Zr past ~5% does not help further.

**Oxygen substitution is worth investigating.** The model predicts even small O content pushes Tc meaningfully above the Zr ceiling. Whether trace oxygen is substituting into the A15 phase rather than forming a separate oxide is an open experimental question.

---

## Limitations

- Structural descriptors such as lattice parameters, space group encoding,
  or bond lengths are required to distinguish polymorphs of the same element
- Phase stability is not modeled — predictions at high substitution fractions
  are extrapolations
- The model struggles above 125 K where cuprate physics departs from BCS theory
- A next step would be SHAP-based inverse analysis for a more rigorous
  decomposition of feature contributions

---

## Stack

- Python 3.11
- pandas, NumPy, matplotlib
- scikit-learn
- ucimlrepo
- SQLite (in-memory via `sqlite3`)

Install dependencies:

```bash
pip install pandas numpy matplotlib scikit-learn ucimlrepo
```

---

## Acknowledgments

Dataset: K. Hamidieh, "A data-driven statistical model for predicting the critical temperature of a superconductor," *Computational Materials Science*, 2018. UCI ML Repository ID 464.
