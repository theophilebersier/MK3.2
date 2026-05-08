# Prusa MK3 "MK_Twin" Project - X-Axis 500mm

Welcome to the official documentation for the **MK_Twin** project. 
This repository contains the modified firmware for a Prusa MK3 (or MK3S/+) to extend its X-axis to **500 mm**, with a dual motor setup.

*(Based on the Original Prusa i3 source code - GNU GPL 3).*

---

## 1. Mechanical Specifications of the Machine
* **Usable print volume (X, Y, Z):** 500 x 250 x 210 mm
* **Y-axis motorization:** Dual NEMA 17 stepper motors (wired in parallel on the same driver).
* **Heated bed:** 2x Original V2 magnetic heatbeds.
* **Bed Leveling:** Smart Prusa system (G80), automatic grid extended to 470 mm.

---
## 2. Mechanical Parts modified and added
* **MOSFET for the controle of the two heatbed:** The motherboard doesn't have the capacity to support the current of two beds, so we need to use two MOSFETs. We have used IRLZ44N. It is not necessary to use this one, but it is necessary to have one that has a continuous drain current of a minimum of 24 Amperes and a drain-to-source voltage of 24V. For the wiring, we have to connect the gates of the two MOSFETs in parallel with the bed output +. The sources of the two MOSFETs are connected in parallel to the ground. The drains are connected to the grounds of the beds. The ground of bed one is connected to one MOSFET, and the ground of the other bed to the other MOSFET; just in series.


---

## 2. Firmware Modifications
For future reference, here are the exact values that were modified compared to the factory Prusa firmware to adapt the brain to the new mechanics:

### `MK3.h & MK3S.h` in variants Files (Limits and Power)
* **Physical dimensions:**
  * `X_MAX_POS` changed to `500`
* **Motor current (TMC2130 Drivers):**
  * To compensate for the weight of the two beds, the holding (`_H`) and running (`_R`) current of the Y-axis has been increased to handle two motors in parallel (approximately 390mA per motor).
  * `TMC2130_CURRENTS_H`: `{16, 35, 35, 30}` *(Y goes from 20 to 35)*
  * `TMC2130_CURRENTS_R`: `{16, 35, 35, 30}` *(Y goes from 20 to 35)*

### `mesh_bed_calibration.h` File (Pinda Probing Area)
* The virtual limits within which the probe is allowed to trigger have been expanded.
* The probing grid spacing is calculated automatically.
  * `BED_Xn` changed to `470.f` (500mm - 30mm safety margin)

---

## 3. Compilation Guide (VS Code & CMake)

The Prusa firmware uses **CMake**.

### Prerequisites
* Visual Studio Code with the **CMake Tools** extension installed.
* Python installed on the PC.

### Compilation procedure
1. **IMPORTANT ON WINDOWS:** Place the `MK3.2` folder as close to the root of your drive as possible (e.g., `C:\MK3.2`). **NEVER** compile from a "OneDrive" folder, as the Windows character limit will cause the compiler to crash with the error `CreateProcess: No such file or directory`.
2. Open the `MK3.2` folder in Visual Studio Code.
3. Open a terminal in VS Code and run the dependency installation with:
   `python .\utils\bootstrap.py`
4. On the left sidebar, open the **CMake** tab > **Build Targets**.
5. Look for the **`ALL_ENGLISH`** target (to compile quickly without European translations) and click the **Build** button.
6. The compiled file will be located in `build/MK3S_MK3S+_..._ENGLISH.hex`. *(Flash it using PrusaSlicer).*

---

## 4. PrusaSlicer Configuration

The firmware does all the work. The modifications in the Slicer are minor:

1. Use a standard `Original Prusa i3 MK3S & MK3S+` or `Original Prusa i3 MK3` profile.
2. Go to **Printer Settings** > **General** > **Bed shape**.
3. Set the dimensions to **X: 500** and **Y: 250** (depending on your plates).
4. **Start G-Code:** Keep the original `G80`. The firmware will automatically stretch the probing across the entire 500 mm plate. The purge line will also be drawn in its original correct location.
5. **Saving:** Go to *File > Export > Export Config Bundle* to save your custom profiles.


---

## 5. Credits & Acknowledgements

The 3D printed parts for this project utilize the work of the 3D printing community. 

* **X-Axis Belt Tensioner & Motor Mount:** To ensure maximum rigidity for the 500mm axis, the MK_Twin project uses the excellent original design by **Boogie** on Printables.
  * Original model: [X-Axis revision with belt tensioner](https://www.printables.com/model/56241-x-axis-revision-with-belt-tensioner)
