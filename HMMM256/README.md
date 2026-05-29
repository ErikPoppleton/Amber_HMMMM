# 256-lipid HMMM example

This directory contains an example of converting a CHARMM-format HMMM from CHARMM-GUI's [HMMM builder](https://www.charmm-gui.org/?doc=input/membrane.hmmm). to Amber Lipid21 format and simulating it with GROMACS.

1. CHARMM and Amber forcefields use different names for most molecules, including waters and ions. We need to rename atoms which will not be fixed by `charmmlipid2amber.py`. Note that the output files from CHARMM-GUI have the name of the lipid you're mimicing with the HMMM, so the lipid name (DOPC, in this case) may be different in your specific file.
```shell
sed -e "s/DOPC/DVPC/g" -e "s/WAT/TP3/g"  HMMM_charmm.pdb > input.pdb
```

2. Using the `charmmlipid2amber.py` script found in the Amber distribution and the charmmlipid2amber.csv file in the parent directory, convert the file to Amber convention:
```shell
python ${AMBERHOME}/bin/charmmlipid2amber.py -c ../charmmlipid2amber.csv -i input.pdb -o input_amber.pdb
```

3. We do not have .atp and .rtp files required by `gmx pdb2gmx`, so we need to manually prepare the topology and simulation box. Let's start with the box. The output from CHARMM-GUI is already solvated so don't need to expand the box:
```shell
gmx editconf -f input_amber.pdb -o box.pdb -d 0
```

4. `charmmlipid2amber.py` does not fix names in the topology file. Also need to update the path to the forcefield:
```shell
sed -e "s/toppar/HMMM.ff/g" -e "s/DOPC/DVPC/g" -e "s/SOD/Na+/g" -e "s/CLA/Cl-/g" -e "s/TIP3/TP3/g" topol_charmm.top > topol.top
```

5. We should now be able to generate a GROMACS minimization file after linking the forcefield:
```shell
ln -s ../HMMM.ff .
gmx grompp -f em.mdp -p topol.top -c box.pdb -o em.tpr
```

6. Begin energy minimization:
```shell
gmx mdrun -deffnm em
```

7. Make an index file because you'll need it for temperature coupling:
```shell
{ echo -e "2|3|4"; echo -e "name 10 MEMB"; echo -e "!10"; echo -e "name 11 SOLV"; echo -e "a P31"; echo -e "q"; } | gmx make_ndx -f em.gro -o index.ndx
```

From this point, the Gromacs relaxation and production files from CHARMM-GUI or your own workflow can be used to continue the run. You will need to add the DCLE molecules to either the membrane or to their own temperature coupling group.