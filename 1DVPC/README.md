# Minimal example: 1DVPC in vacuum

This directory contains a minimal example of converting a DVPC molecule from CHARMM to Amber format and performing energy minimization in GROMACS.

The parent folder contains an extendend version of the charmmlipid2amber.csv file which is used by Amber's charmmlipid2amber.py to map CHARMM nomenclature to Amber. The script can be found in `${AMBERHOME}/bin/` of the Amber distribution. To convert the 1DVPC_charmm.pdb file included here:

```shell
python ${AMBERHOME}/bin/charmmlipid2amber.py -c ../charmmlipid2amber.csv -i 1DVPC_charmm.pdb -o 1DVPC_amber.pdb
```

Next, add a box so Gromacs can work with it:
```shell
gmx editconf -f 1DVPC_amber.pdb -o box.pdb -d 2
```

We do not have the .atp and .rtp files required by `pdb2gmx`, but that actually doesn't matter because the topology was trivial to manually generate. You can still use `grompp` to prepare for energy minimization:
```shell
gmx grompp -f em.mdp -p topol_1DVPC.top -c box.pdb -o 1DVPC_em.tpr
```

This should not produce any errors and can be minimized in a few seconds with:
```shell
gmx mdrun -s 1DVPC_em.tpr
```