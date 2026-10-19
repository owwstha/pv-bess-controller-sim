# Residential PV-BESS Controller Simu

Python-based simulation modeling the energy dispatch, zero-export control, islanded operation, and cost savings of a residential Solar PV + Battery Energy Storage System (BESS). 

---

## Objectives

Simulate and evaluate how a residential solar-plus-storage system behaves over a typical year under real-world constraints. The simulation tests energy management strategies to minimize electricity bills while ensuring compliance with local grid codes.

### Key Objectives:
- **Self-Consumption Optimization:** Maximize usage of on-site solar generation to power household loads and recharge the battery.
- **Outage Resilience (Islanding):** Simulate grid loss events where the system disconnects via the Main Circuit Breaker (CB) and transitions to an off-grid microgrid.

---

## System Components 

The simulation models the following hardware components and operational logic in SIMULINK:

1. **Solar PV Array with MPPT:**
   - Reads an annual (8,760 hours) real-world solar irradiance dataset (`.xlsx`).
   - Converts irradiance and temperature into usable DC generation power.

2. **Battery Energy Storage System (BESS):**
   - Tracks State of Charge and nominal capacity.
   - Enforces operational bounds ($SoC_{min}$, $SoC_{max}$) and continuous C-rate limits to prevent accelerated degradation.

3. **Dual-Mode Hybrid Inverter:**
   - **Grid-Tied Mode:** Synchronizes with the grid. Feeds local loads and manages battery charging while throttling solar if generation exceeds total demand and battery capacity.
   - **Islanded Mode:** When the Main CB opens, the inverter sets local voltage and frequency, supplying household critical loads using available solar and stored battery energy alone.

---

## Control Logic

For each hourly time step ($\Delta t = 1\,\text{h}$):

- **If $P_{solar} > P_{load}$:**
  1. PV powers the load directly.
  2. Excess power charges the BESS (up to its maximum charge rate and $SoC_{max}$).
  3. Any leftover generation is **curtailed** to prevent exporting to the grid.
  
- **If $P_{load} > P_{solar}$:**
  1. PV supplies what it can.
  2. The deficit is discharged from the BESS (down to $SoC_{min}$).
  
- **If BESS is Empty ($SoC \le SoC_{min}$):**
  - **Grid-Tied:** The remaining balance is imported from the grid ($P_{grid} > 0$).
  - **Islanded (Outage):** Gr
