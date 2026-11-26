# IEEE Conference Paper Writing Guidelines (IEEEtran)

Instructions for writing the LaTeX paper with the IEEE conference template.

## Document class
Use `\documentclass[conference]{IEEEtran}`. `IEEEtran.cls` and `IEEEtran.bst` are part of
the standard TeX Live distribution (`collection-publishers`) and are preinstalled on
Overleaf, so the build environment must have a full TeX Live (or use Overleaf). They are
intentionally NOT bundled in this folder.

## Language
All manuscripts must be in English.

## Format
- Two-column IEEE conference format (handled by the document class).
- Keep an `\begin{abstract}...\end{abstract}` and an `\begin{IEEEkeywords}...\end{IEEEkeywords}` block.

## Paper length
Follow your target venue's Call for Papers. IEEE conferences commonly allow **6–8 pages**;
for many IEEE venues the references **do** count toward the page limit (unlike CVPR), so
check the specific CFP (e.g., IEEE EMBC, BHI, BSN) before finalizing.

## Mathematics
Number sections and displayed equations:
```latex
\begin{equation}
  \mathrm{RMSSD} = \sqrt{\tfrac{1}{N-1}\sum_{i=1}^{N-1}(RR_{i+1}-RR_i)^2}
  \label{eq:rmssd}
\end{equation}
```

## Cross-references
Use `cleveref`'s `\cref{...}` / `\Cref{...}` for figures, tables, equations, and sections,
e.g. `\cref{fig:models}`, `\cref{tab:results}`, `\cref{eq:rmssd}`.

## Tables (booktabs)
```latex
\begin{table}[t]
  \centering
  \begin{tabular}{@{}lccc@{}}
    \toprule
    Model & Accuracy & Macro-F1 & AUROC \\
    \midrule
    mean-HR baseline & \dots & \dots & \dots \\
    Random Forest    & \dots & \dots & \dots \\
    \bottomrule
  \end{tabular}
  \caption{LOSO results on WESAD (baseline vs stress).}
  \label{tab:results}
\end{table}
```

## Figures
```latex
\begin{figure}[t]
  \centering
  \includegraphics[width=\linewidth]{figures/fig_models.png}
  \caption{Macro-F1 by model under LOSO cross-validation.}
  \label{fig:models}
\end{figure}
```

## Important Rules
* Do NOT rename `\usepackage[capitalize]{cleveref}` to `cleverref` — there is no `cleverref.sty`.
* Keep the existing packages in the template unchanged.
* All figures reside in the `figures/` directory. **Always** include the `figures/` prefix
  in `\includegraphics` (e.g., `figures/fig_models.png`, not `fig_models.png`), or the
  build will fail to find the image.
* Use the bibliography style `IEEEtran` (`\bibliographystyle{IEEEtran}`).
* Always use the following author-information placeholder:
    * Ambitious AI Researcher, AI Research Institute, 123 AI Avenue, ML City, researcher@institute.ai
