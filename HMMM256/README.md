# 256-lipid HMMM example

This directory contains an example of converting a CHARMM-format HMMM from CHARMM-GUI's [HMMM builder](https://www.charmm-gui.org/?doc=input/membrane.hmmm). to Amber Lipid21 format and simulating it with GROMACS.

1. CHARMM and Amber forcefields use different names for most molecules, including waters, ions, and 1,1 dichloroethane. We need to rename atoms which will not be fixed by `charmmlipid2amber.py`. Requires two `sed` calls because the carbons in DCLE have inverted names (C1/C2) in the two forcefield files. Note that the output files from CHARMM-GUI have the name of the lipid you're mimicing with the HMMM, so the lipid name (DOPC, in this case) may be different in your specific file.
```shell
sed -e "s/DOPC/DVPC/g" -e "s/WAT/TP3/g" -e "s/C1  DC/XX  DC/g" -e "s/CL11/ CL3/g" -e "s/CL12/ CL4/g" -e "s/H13 D/H8  D/g" -e "s/C2  DC/YY  DC/g" -e "s/H21/H5 /g" -e "s/H22/H6 /g" -e "s/H23/H7 /g"  HMMM_charmm.pdb > input.pdb; 
sed -i -e "s/XX  DC/C2  DC/g" -e "s/YY  DC/C1  DC/g"  input.pdb
# Or if you're on MacOS instead of Linux: sed -i '' -e "s/XX  DC/C2  DC/g" -e "s/YY  DC/C1  DC/g"  input.pdb
```

2. Using the `charmmlipid2amber.py` script found in the Amber distribution and the charmmlipid2amber.csv file in the parent directory, convert the file to Amber convention:
```shell
python ${AMBERHOME}/bin/charmmlipid2amber.py -c ../charmmlipid2amber.csv -i input.pdb -o input_amber.pdb
```

3. We do not have .atp and .rtp files required by `gmx pdb2gmx`, so we need to manually prepare the topology and simulation box. Let's start with the box:
```shell
gmx editconf -f input_amber.pdb -o box.pdb -d 0
```

4. `charmmlipid2amber.py` does not fix the topology file, so we need to fix that too:
```shell
sed -e "s/toppar/..\/HMMM.ff/g" -e "s/DOPC/DVPC/g" -e "s/SOD/Na+/g" -e "s/CLA/Cl-/g" -e "s/TIP3/TP3/g" topol_charmm.top > topol.top
```

5. We should now be able to generate a GROMACS minimization file:
```shell
gmx grompp -f em.mdp -p topol.top -c box.pdb -o em.tpr
```

6. Begin energy minimization:
```shell
gmx mdrun -deffnm em
```

From this point, the Gromacs relaxation and production files from CHARMM-GUI can be used to continue the run. You will need to add the DCLE molecules to either the membrane or to their own temperature coupling group.