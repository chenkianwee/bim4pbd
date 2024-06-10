# Radiant Heat Transfer: Reflectance, Absorptance and Emittance
These notes are based on these sources:
- Architectural science textbook {cite}`szokolay_introduction_2014` Pg 10-11.
<Br></Br>
- **Reflectance $\rho$**: decimal fraction indicating how much of the incident radiation is reflected by a surface.
- **Absorptance $\alpha$**: decimal fraction, a measure of the ability to absorb radiation, relative to the perfect black body where the absorptance is 1. 
  - **For any opaque surfaces reflectance + absorptance = 1**. 
  - Absortance is high for dark surface and low for light/shiny metallic surfaces. e.g. 0.9 for black asphalt, 0.2 for shiny aluminium.
- **emittance $\epsilon$**: decimal fraction, a measure of the ability to emit radiation, relative to the ‘black body’, the perfect emitter.
  - **For ordinary surfaces emittance = absorptance** for the same wavelength (temperature) of radiation. However many surfaces have selective properties. For example: 
  - it is desireable for solar collectors to have high absorptance for solar (6000C), but low emittance at ordinary temperatures (<100c).
  - it is desireable for skyview passive cooling to have low absorptance for solar (6000C), but high emittance at ordinary temperatures (<100c).
  - A shiny metal surface is non-selective meaning emittance = absorptance.
- **Transmittance**: decimal fraction indicating how much of the incident radiation is transmitted through the surface. Usually for transparent materials like glass.

## Calculate Radiative Heat Transfer between Two Surfaces
- ASHRAE handbook  heating, ventilating, and air-conditioning systems and equipment, Chapter 6.2

- qr = s * Fr * (Tp**4 - Tr**4)
  - qr = radiative heat transfer (W)
  - s = Stefan-Boltzmann constant = 5.67 * 10–8 W/(m2·K4)
  - Fr = radiation exchange factor (dimensionless) 
  - Tp = effective temperature of heating (cooling) panel surface, K 
  - Tr = temperature of fictitious surface (unheated or uncooled), K

- Fr = 1/((1/Fpr) + (1/Ep - 1) + (Ap/Ar)*(1/Er-1))
  - Fpr = radiation angle factor from panel to fictitious surface (1.0 for flat panel) (View factor) 
  - Ap, Ar = area of panel surface and fictitious surface, respectively 
  - Ep , Er = thermal emittance of panel surface and fictitious surface, respectively (dimensionless) (in a room is usually 0.9)

## Solar Radiation, Irradiance, Irradiation, Insolation
- Solar radiation, often called the solar resource or just sunlight, is a general term for the electromagnetic radiation emitted by the sun.
  - https://www.energy.gov/eere/solar/solar-radiation-basics
- There are many different words and meanings such as solar radiation (electromagnetic), solar irradiance (for power), solar irradiation (for energy), as well as solar insolation to describe the amount of sunlight that is available at any particular location.
  - https://www.alternative-energy-tutorials.com/solar-power/solar-irradiance.html
- Solar Irradiance (W/m2): the amount of solar power landing on a surface
- Solar Irradiation (kWh/m2): this is the same as Insolation. Integration of Solar Irradiance. The SI unit is Joule per sqm (J/m2).


## Instruments to measure radiative heat transfer
  - Pyrgeometer (https://en.wikipedia.org/wiki/Pyrgeometer)
    - measures near-surface infra-red (IR) radiation, approximately from 4.5 μm to 100 μm on the electromagnetic spectrum (thereby excluding solar radiation).
  - Pyranometer (https://en.wikipedia.org/wiki/Pyranometer)
    -  measuring solar irradiance on a planar surface and it is designed to measure the solar radiation flux density (W/m2) from the hemisphere above within a wavelength range 0.3 μm to 3 μm.