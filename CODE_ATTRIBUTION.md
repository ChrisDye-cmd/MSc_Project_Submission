# Code Attribution

This note records where the analysis uses third-party library routines, and the
two places where a standard algorithm was reimplemented by hand rather than called
from a library. Routine data-handling and plotting (NumPy, pandas, Matplotlib,
Seaborn) are used throughout and are not listed individually.

## Libraries providing the main algorithms

- **statsmodels** — the Augmented Dickey-Fuller unit-root test (`adfuller`), the
  Engle-Granger cointegration test with MacKinnon critical values (`coint`), and
  ordinary least squares (`OLS`). Notebook 03.
- **hmmlearn** — the Gaussian hidden Markov model: parameter estimation and the
  Viterbi most-likely-path decode (`GaussianHMM`). Notebook 04.
- **scikit-learn** — feature standardisation (`StandardScaler`). Notebook 04.
- **SciPy** — numerically stable log-sum-exp (`scipy.special.logsumexp`), used in
  the hand-written forward algorithm below; `scipy.optimize.minimize` imported for
  the baseline. Notebook 04.
- **NumPy / pandas / Matplotlib / Seaborn** — arrays, dataframes, timestamp
  handling, resampling, and all figures, throughout.

References

- Harris, C. R., et al. (2020). Array programming with NumPy. Nature, 585, 357–362. https://doi.org/10.1038/s41586-020-2649-2
- McKinney, W. (2010). Data Structures for Statistical Computing in Python. Proceedings of the 9th Python in Science Conference, 56–61. https://doi.org/10.25080/Majora-92bf1922-00a
- John D. Hunter. 2007. Matplotlib: A 2D Graphics Environment. Computing in Science and Engg. 9, 3 (May 2007), 90–95. https://doi.org/10.1109/MCSE.2007.55
- Waskom, M. L., (2021). seaborn: statistical data visualization. Journal of Open Source Software, 6(60), 3021, https://doi.org/10.21105/joss.03021
- Virtanen, P., Gommers, R., Oliphant, T.E. et al. SciPy 1.0: fundamental algorithms for scientific computing in Python. Nat Methods 17, 261–272 (2020). https://doi.org/10.1038/s41592-019-0686-2
- Seabold, S. & Perktold, J. (2010). Statsmodels: Econometric and Statistical Modeling with Python. SciPy 2010, https://doi.org/10.25080/Majora-92bf1922-011
- Fabian Pedregosa, Gaël Varoquaux, Alexandre Gramfort, Vincent Michel, Bertrand Thirion, Olivier Grisel, Mathieu Blondel, Peter Prettenhofer, Ron Weiss, Vincent Dubourg, Jake Vanderplas, Alexandre Passos, David Cournapeau, Matthieu Brucher, Matthieu Perrot, and Édouard Duchesnay. 2011. Scikit-learn: Machine Learning in Python. J. Mach. Learn. Res. 12, null (2/1/2011), 2825–2830.
- hmmlearn developers. hmmlearn: Hidden Markov Models in Python with a scikit-learn-like API. https://github.com/hmmlearn/hmmlearn (version 0.3.3)