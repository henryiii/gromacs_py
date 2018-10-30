[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.1455734.svg)](https://zenodo.org/record/1455734)

# Gromacs_py

Gromacs_py is a python library allowing a simplified use of the gromacs MD simulation software. Gromacs_py can build topologie based on a pdb file, create the simulation system (box, add water and ions) and run minimisation, equilibration and production run.  
One of the main objective of the gromacs_py wrapper is to automatize routine operations for MD simulation of multiple systems. 


## Quick install

Get gromacs_py library from [github](https://github.com/samuelmurail/gromacs_py).

```bash
git clone https://github.com/samuelmurail/gromacs_py.git
cd gromacs_py
./setup.py install
```
see the [INSTALL.md](./INSTALL.md) file for details.


## Tutorial:

Here is an example of a short simulation of the SH3 domain of phsopholipase Cγ1.  
Seven successive steps are used:

1. Topologie creation using `create_top.py`.
2. Minimisation of the structure using `minimize_pdb.py`.
3. Solvation of the system using `solvate_ions.py`.
4. Minimisation of the system using `minimize_pdb.py`.
5. Equilibration of the system using `equi_3_step.py`.
6. Production run using `production.py`.
7. Extension of the production run using `extend.py`.

```bash
# Create topologie 
gromacs_py/create_top.py -f gromacs_py/test/input/1y0m.pdb  -o tmp/1y0m/top -vsite

# Minimize the protein structure
gromacs_py/minimize_pdb.py -f tmp/1y0m/top/1y0m_pdb2gmx_box.pdb -p tmp/1y0m/top/1y0m_pdb2gmx.top -o tmp/1y0m/em/  -n em_1y0m -nt 2

# Add water and ions
gromacs_py/solvate_ions.py -f tmp/1y0m/em/em_1y0m_compact.pdb -p tmp/1y0m/top/1y0m_pdb2gmx.top -o tmp/1y0m_water_ions/top/  -n 1y0m_water_ions

# Minimize the system
gromacs_py/minimize_pdb.py -f tmp/1y0m_water_ions/top/1y0m_water_ions_water_ion.gro -p tmp/1y0m_water_ions/top/1y0m_water_ions_water_ion.top -o tmp/1y0m_water_ions/em/  -n em_1y0m

# Do three small equilibrations with postion contraints on heavy atoms (first), Carbon alpha (second) and low constraint on Carbon alpha (third)
gromacs_py/equi_3_step.py -f tmp/1y0m_water_ions/em/em_1y0m_compact.pdb -p tmp/1y0m_water_ions/top/1y0m_water_ions_water_ion.top -o tmp/1y0m_water_ions/  -n 1y0m -HA_time 0.1 -CA_time 0.1 -CA_LOW_time 0.1

# Small production run of 0.1 ns
gromacs_py/production.py -f tmp/1y0m_water_ions/02_equi_CA_LOW/equi_CA_LOW_1y0m.gro -p tmp/1y0m_water_ions/top/1y0m_water_ions_water_ion.top -o tmp/1y0m_water_ions/03_prod -n 1y0m -time 0.1

# Extension of the simulation
gromacs_py/extend.py -s tmp/1y0m_water_ions/03_prod/prod_1y0m.tpr -time 0.2

# Remove simulation files
rm -r ./tmp
```

## Authors

* **Samuel Murail** - [Université Paris Diderot](https://www.univ-paris-diderot.fr), [MTi lab](http://www.mti.univ-paris-diderot.fr/) 

See also the list of [contributors](https://github.com/samuelmurail/gromacs_py/contributors) who participated in this project.

## License

This project is licensed under the GNU General Public License v2.0 - see the [LICENSE](./LICENSE) file for details.
