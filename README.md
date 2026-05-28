# Amber-format HMMM parameters for GROMACS
Conversion utilities and Gromacs forcefield files for the Highly Mobile Membrane Mimetic Model (HMMM) in Amber Lipid21.

## Contents
- `1DVPC/` - Minimal example of converting a single DVPC molecule from CHARMM to Amber convention and minimizing it with GROMACS using Lipid21.
- `HMMM.ff/` - Amber Lipid21 parameters for the Highly Mobile Membrane Mimetic Model (HMMM). DVPC parameters derived from DLPC (shortest lipid included in the original forcefield), 1,1-DCLE parameters from [Ligandbook](https://ligandbook.org/package/2729)
- `HMMM256/` - An example of converting a CHARMM-format HMMM with 256 lipids from CHARMM-GUI's [HMMM builder](https://www.charmm-gui.org/?doc=input/membrane.hmmm).
- `charmmlipid2amber.csv` - the translation file used by Amber's charmmlipid2amber.py updated with parameters for 5-carbon lipids
- `charmmlipid2amber_dvpc.csv` - The lines appended to the core `charmmlipid2amber.csv`. If you already made modifications to your `charmmlipid2amber.csv`, you can simply append this to the end:
```shell
cat charmmlipid2amber_dvpc.csv >> charmmlipid2amber.csv
```

## Concerns
Previous studies employing the HMMM and the CHARMM36 forcefield found that it was necessary to include position restraints on the lipid headgroups to maintain membrane integrity. In our studies of these lipids, while the Amber Lipid21 forcefield resulted in a HMMM which was stable for longer than HMMM, without position restraints, the membrane would often still breakup after 1-2 µs of simulation time.

## Citations
**HMMM:**  
Ohkubo, Y. Z., Pogorelov, T. V., Arcario, M. J., Christensen, G. A. & Tajkhorshid, E. Accelerating membrane insertion of peripheral proteins with a novel membrane mimetic model. Biophysical journal 102, 2130–2139 (2012). https://doi.org/10.1016/j.bpj.2012.03.015

**CHARMM-GUI HMMM builder:**  
Qi, Y. et al. Charmm-gui hmmm builder for membrane simulations with the highly mobile membrane-mimetic model. Biophysical journal 109, 2012–2022 (2015). https://doi.org/10.1016/j.bpj.2015.10.008

**this repository:**  
*coming soon*

