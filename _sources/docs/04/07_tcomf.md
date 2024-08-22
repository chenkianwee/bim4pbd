# Thermal Comfort
- ASHRAE 55
    - https://www.simscale.com/blog/what-is-ashrae-55-thermal-comfort/

- ISO 7730
    - https://www.iso.org/standard/39155.html

- https://www.linkedin.com/pulse/role-cfd-evaluating-occupant-thermal-comfort-sandip-jadhav/

- Calculation of PMV and PPD: https://www.ergo.human.cornell.edu/studentdownloads/DEA3500notes/Thermal/thcomnotes2.html 

## Predicted Mean Vote (PMV) and Predicted Percentage of Dissatsfied (PPD)
Fanger's Comfort Equation This equation contains terms which relate to:

- functions of clothing: 
    - Icl = clothing insulation in clothes
    - fcl = ratio of clothed/nude surface area
- functions of activity: 
    - H = Metabolic heat production (w/m2)
    - M = Metabolic free energy production (external work)(w/m2)
- Environmental variables: 
    - Ta = Air temperature (°C)
    - Tr = Mean radiant temperature (°C)
    - v = Relative air speed (m/s)
    - Pa = Vapor pressure of water vapor (mb)

- PMV is a 7 point scale where -3 is too cold and +3 is too hot. 0 is neutral.
    - PMV = 4 + (0.303 exp(-0.036H) + 0.0275) x {6.57 + 0.46H + 0.31Pa + 0.0017HPa + 0.0014HTa - 4.13 fcl (1 + 0.01dT) (Tcl - Tr) - hcfcl (Tcl - Ta)}
        - where Tcl (surface temperature of clothed body) = 35.7 - 0.0275H + 0.155 Iclofcl (4.13 (1 + 0.Old Temp) 1 + 0.155 Iclofcl (4.13 (1 + 0.Old Temp)thc
        - where hc = 2.4(Tcl - Ta)0.25 or 12.1 square root of v (air speed) which is greater and dT = Tr - 22.

- PPD is a function of PMV expressed as a percentage of people dissatisfied.
    - PPD = 100 - (95*exp(-0.03353*PMV<sup>4</sup> - 0.2179*PMV<sup>2</sup>)
