# 2+1D Quantum Gravity — Tabletop Analogue Experiments

Based on Carlip (2005): *Quantum Gravity in 2+1 Dimensions: The Case of a Closed Universe*

Each experiment maps a core mathematical structure from 2+1D quantum gravity onto a physically realisable system.
Start with Experiments 3, 5 (cheapest, easiest) then progress to 1 (most relevant to memR).

---

## Table of Contents

1. [Holonomy / Geometric Phase — Fibre Optic Berry Phase](#exp1)
2. [Chern-Simons — Photonic Topological Edge Modes](#exp2)
3. [ADM Moduli — Vibrating Membrane Modes](#exp3)
4. [Cauchy Horizon — Acoustic Black Hole in Flowing Water](#exp4)
5. [Wheeler-DeWitt Tunnelling — LC Circuit / Tunnel Diode](#exp5)

---

<a name="exp1"></a>
## Experiment 1 — Holonomy / Geometric Phase (Berry Phase)

### What this tests

In 2+1D gravity, a **holonomy** is the transformation accumulated by a vector (or spinor) transported around a closed loop in a curved or topologically non-trivial spacetime. It is the gravitational degree of freedom — when there are no local gravitons, holonomies *are* the physics.

Mathematically, for a connection $A$ around loop $\gamma$:

$$h_\gamma(A) = \mathcal{P}\exp\left(i\oint_\gamma A_\mu\, dx^\mu\right)$$

For U(1) (electromagnetism / optical polarisation), this reduces to the **Berry phase**:

$$\gamma_B = i \oint \langle \psi(\mathbf{R}) | \nabla_\mathbf{R} | \psi(\mathbf{R}) \rangle \cdot d\mathbf{R}$$

For a photon with polarisation state $|\psi\rangle$ transported around a closed path on the Poincaré sphere (parameterised by propagation direction $\hat{k}$), the Berry phase equals the **solid angle** subtended by the path:

$$\gamma_B = -\Omega/2$$

where $\Omega$ is the solid angle. This is the **Pancharatnam–Berry phase**.

**For a helical fibre coil** of $N$ turns, pitch angle $\alpha$, the solid angle is:

$$\Omega = 2\pi N (1 - \cos\alpha)$$

So the expected Berry phase per turn:

$$\gamma_B = -\pi(1 - \cos\alpha) \quad \text{per turn}$$

This is a **pure geometric phase** — independent of the speed of light, wavelength, or fibre material. It is a holonomy of the U(1) spin connection.

### Connection to memR

Each MZI phase shift in your memR network is a U(1) holonomy. A deep memR network computes the **product of holonomies** along each path — identical in structure to the path-ordered exponential above. Measuring geometric phase in fibre directly validates the mathematical foundation of memR's computation.

---

### What to Build

```
[Laser] → [Polariser (45°)] → [Fibre coil, N turns, pitch α] → [Analyser] → [Photodetector] → [Oscilloscope/DMM]
```

**Parts list:**

| Item | Spec | Approx cost |
|---|---|---|
| Laser diode module | 650nm, 5mW, collimated | $8 |
| Single-mode optical fibre | Patch cable, FC/PC, ≥2m | $15 |
| Linear polarisers ×2 | Film or glass, 25mm | $12 |
| Quarter-wave plate | 650nm, 25mm | $18 |
| Photodetector | OPT101 or BPW34 + TIA | $5 |
| Oscilloscope or DMM | Any | owned |
| Mandrel / coil former | 3D print or cardboard tube | $0 |
| Optical rail / bench | Optical posts or improvised | $20 |

**Total: ~$80**

---

### Build Steps

#### Stage 1 — Basic interferometer (no coil)

1. Mount laser → polariser (0°) → analyser (0°) → detector in a straight line on an optical rail.
2. Rotate analyser from 0° to 180° in 10° steps. Record detector voltage.
3. **Expected:** Malus's Law: $I = I_0 \cos^2\theta$. Fit with Python.
4. **Pass criterion:** $R^2 > 0.99$ on cosine fit.

```python
import numpy as np
from scipy.optimize import curve_fit
import matplotlib.pyplot as plt

theta = np.arange(0, 181, 10) * np.pi / 180
I_measured = np.array([...])  # fill from measurements

def malus(theta, I0, offset):
    return I0 * np.cos(theta)**2 + offset

popt, _ = curve_fit(malus, theta, I_measured)
print(f"I0 = {popt[0]:.4f}, offset = {popt[1]:.4f}")
```

**Fault finding:**
- Signal too noisy → shield from ambient light, use red laser not white LED
- No modulation → polarisers not aligned to beam axis; check both faces
- Signal saturates → reduce laser power with ND filter or increase TIA gain resistor

---

#### Stage 2 — Coil the fibre

1. Wind N=10 turns of fibre onto a 30mm diameter mandrel at pitch angle α ≈ 20°.
2. Insert coil between polariser and analyser.
3. Set polariser and analyser both to 45°. Record detector voltage $V_0$ (reference, no geometric phase offset expected yet).
4. Change analyser angle in 5° steps from 0° to 180°. Find the angle $\theta_{\min}$ of minimum transmission.

**Expected Berry phase:**

$$\gamma_B = -\pi(1-\cos 20°) \times 10 \approx -\pi \times 0.0603 \times 10 \approx -1.894 \text{ rad} \approx -108.5°$$

So the analyser minimum should shift from 90° to approximately 90° − 108.5°/2 ≈ 36°.

**Fault finding:**
- Phase shift missing → fibre is multimode (use SMF-28 or equivalent single-mode patch cable)
- Phase shift present but wrong magnitude → recount turns; measure pitch angle more carefully with a ruler and $\alpha = \arctan(\text{pitch}/\pi d)$
- Fringe visibility low → check fibre connectors are clean; use FC/APC if available

---

#### Stage 3 — Vary N and α

1. Rewind fibre with N = 5, 10, 15, 20 turns at fixed α.
2. For each N, measure the phase shift.
3. Plot $\gamma_B$ vs $N$ — expect linear relationship with slope $-\pi(1-\cos\alpha)$.

```python
N_turns = np.array([5, 10, 15, 20])
gamma_measured = np.array([...])  # in radians

slope_theory = -np.pi * (1 - np.cos(np.radians(20)))
slope_measured = np.polyfit(N_turns, gamma_measured, 1)[0]

print(f"Theory slope: {slope_theory:.4f} rad/turn")
print(f"Measured slope: {slope_measured:.4f} rad/turn")
print(f"Discrepancy: {abs(slope_theory-slope_measured)/abs(slope_theory)*100:.1f}%")
```

4. Repeat for α = 10°, 20°, 30°, 45°. Plot $\gamma_B/N$ vs $\cos\alpha$ — expect $-\pi + \pi\cos\alpha$.

**Pass criterion:** measured slope within 10% of theory across at least 3 values of N.

---

#### Stage 4 — Analysis and holonomy interpretation

The Berry phase is the U(1) holonomy of the fibre path. To connect explicitly to the gravity formalism:

The Chern-Simons connection on the Poincaré sphere for a photon state $|\psi\rangle = \cos(\theta/2)|R\rangle + e^{i\phi}\sin(\theta/2)|L\rangle$ is:

$$A_\phi = i\langle\psi|\partial_\phi|\psi\rangle = -\frac{1}{2}\cos\theta$$

The Berry phase around the closed path on the sphere:

$$\gamma_B = \oint A \cdot d\mathbf{l} = \iint_S F \, dS = -\frac{\Omega}{2}$$

where $F = dA$ is the field strength (curvature 2-form). This is the exact same structure as the LQG holonomy — the field strength $F^i_{ab}$ of the Ashtekar connection integrated over a surface.

---

<a name="exp2"></a>
## Experiment 2 — Chern-Simons / Photonic Topological Edge Modes

### What this tests

Chern-Simons theory is the gauge theory underlying both 2+1D gravity and the fractional quantum Hall effect. Its defining topological invariant is the **Chern number**:

$$C = \frac{1}{2\pi}\int_{\text{BZ}} F_{xy}\, dk_x\, dk_y$$

where $F_{xy} = \partial_{k_x} A_{k_y} - \partial_{k_y} A_{k_x}$ is the Berry curvature in momentum space.

A system with $C \neq 0$ has **topologically protected edge modes** — states that propagate along boundaries and cannot be removed by any local perturbation. These are the photonic analogue of the Wilson loop observables in Chern-Simons gravity.

The Chern-Simons action:

$$S_{CS} = \frac{k}{4\pi}\int \text{tr}\left(A \wedge dA + \frac{2}{3}A \wedge A \wedge A\right)$$

### What to Build — Microstrip Topological Waveguide

A 2D array of coupled transmission line resonators with broken time-reversal symmetry (via a ferrite insert or via geometry with non-reciprocal coupling) implements a photonic Chern insulator.

**Simplest version:** a 1D chain of coupled LC resonators with alternating coupling (SSH model), which has $C = \pm 1$ and exhibits a topological edge mode.

**Parts list:**

| Item | Spec | Approx cost |
|---|---|---|
| Inductors | 10µH, through-hole ×20 | $5 |
| Capacitors | 100nF ×20 | $3 |
| Stripboard / protoboard | 160×100mm | $4 |
| Function generator | 1Hz–1MHz | owned / $30 |
| Oscilloscope | 2 channel | owned |
| Resistors | Various | $2 |

**Total: ~$15 (assuming oscilloscope owned)**

---

### Build Steps — SSH Chain

The Su-Schrieffer-Heeger (SSH) model is a 1D chain with alternating couplings $t_1, t_2$:

$$H = \sum_n t_1 c^\dagger_{A,n} c_{B,n} + t_2 c^\dagger_{A,n+1} c_{B,n} + \text{h.c.}$$

In the LC circuit implementation: inductors as sites, capacitors as couplings.

Alternating capacitors $C_1 = 47\text{nF}$ (weak coupling) and $C_2 = 220\text{nF}$ (strong coupling):

$$\omega_{edge} = \frac{1}{\sqrt{L \cdot C_{eff}}}$$

Topological phase exists when $t_2 > t_1$, i.e. $C_2 > C_1$.

#### Stage 1 — Build trivial chain ($C_1 = C_2$)

1. Build 10-site chain: L-C-L-C-...-L with all $C = 100\text{nF}$, $L = 10\mu\text{H}$.
2. Drive one end with function generator, sweep 1kHz–500kHz.
3. Measure amplitude at each site with oscilloscope probe.
4. **Expected:** bulk propagation band, exponential decay at edges, no in-gap state.
5. Record the passband edges $\omega_-$ and $\omega_+$:
$$\omega_\pm = \frac{1}{\sqrt{LC}} \sqrt{1 \pm \frac{C_{coupling}}{C_{site}}}$$

**Fault finding:**
- No propagation → check all solder joints; measure each L and C with an LCR meter
- Resonance shifted → component tolerances; use measured values in simulation
- High loss → use air-core inductors, avoid ferrite at these frequencies if possible

#### Stage 2 — Build topological chain ($C_1 \neq C_2$)

1. Replace capacitors with alternating 47nF / 220nF.
2. Sweep frequency again. Look for a mode **inside the gap** between the two bands.
3. Probe all sites. Edge mode should show amplitude peaked at site 1 and site 10, exponentially decaying toward centre.

```python
import numpy as np
import matplotlib.pyplot as plt

N = 10
t1 = 0.47   # proportional to C1
t2 = 2.20   # proportional to C2

# Build SSH Hamiltonian
H = np.zeros((N, N))
for i in range(0, N-1, 2):
    H[i, i+1] = t1
    H[i+1, i] = t1
for i in range(1, N-1, 2):
    H[i, i+1] = t2
    H[i+1, i] = t2

eigvals, eigvecs = np.linalg.eigh(H)

plt.figure(figsize=(10,4))
plt.subplot(1,2,1)
plt.plot(eigvals, 'o', color='gold')
plt.axhline(0, color='red', ls='--', label='Zero mode (edge state)')
plt.xlabel('Mode index'); plt.ylabel('Energy'); plt.title('SSH Spectrum')
plt.legend()

plt.subplot(1,2,2)
edge_idx = np.argmin(np.abs(eigvals))
plt.bar(range(N), eigvecs[:,edge_idx]**2, color='gold')
plt.xlabel('Site'); plt.ylabel('Probability density')
plt.title('Edge State Wavefunction')
plt.tight_layout(); plt.show()
```

**Pass criterion:** in-gap state visible in spectrum; amplitude at end sites > 10× amplitude at central sites.

**Fault finding:**
- No in-gap state → ratio $t_2/t_1$ not large enough; increase $C_2$ to 470nF
- In-gap state at wrong frequency → remeasure actual component values and recompute expected $\omega_{edge}$
- Both edges show mode → correct, this is expected for open boundaries

---

<a name="exp3"></a>
## Experiment 3 — ADM Moduli / Vibrating Membrane Modes

### What this tests

In the ADM approach to 2+1D gravity, the dynamical variable for a toroidal universe is the **Teichmüller parameter** (modulus) $\tau = \tau_1 + i\tau_2 \in \mathbb{H}$ (upper half complex plane). It describes the shape of the 2D spatial torus.

The Laplacian on a flat torus with modulus $\tau$ has eigenvalues:

$$\lambda_{mn} = \frac{4\pi^2}{\tau_2^2 \, A} |m - n\tau|^2, \quad m,n \in \mathbb{Z}$$

where $A$ is the area. The **normal mode frequencies** of a vibrating membrane on this torus are $\omega_{mn} = c\sqrt{\lambda_{mn}}$.

This is also the spectrum of the Wheeler-DeWitt operator in the mini-superspace approximation — **the resonant modes of the membrane are quantum states of the 2+1D universe**.

---

### What to Build

A rectangular elastic membrane with adjustable aspect ratio.

**Parts list:**

| Item | Spec | Approx cost |
|---|---|---|
| Latex sheet | 0.3mm thick, 300×300mm | $8 |
| Wooden frame | Adjustable aspect ratio | $10 |
| Small speaker (driver) | 8Ω, 0.5W | $4 |
| Microphone | Electret capsule | $3 |
| Arduino Uno | For sweep generation | owned/$25 |
| Cornstarch + water | Chladni pattern visualisation | $2 |

**Total: ~$27**

---

### Build Steps

#### Stage 1 — Build the membrane

1. Cut latex sheet to 200×200mm. Stretch evenly over a square wooden frame and staple.
2. Glue speaker to underside of frame centre.
3. Mount electret microphone 5cm above membrane centre.
4. Connect Arduino DAC (or PWM + RC filter) to speaker amplifier, microphone to Arduino ADC.

#### Stage 2 — Measure resonant frequencies (square membrane)

For a square membrane ($\tau = i$, aspect ratio $b/a = 1$):

$$f_{mn} = \frac{c}{2a}\sqrt{m^2 + n^2}, \quad m,n = 1,2,3,...$$

where $c = \sqrt{T/\sigma}$ is the wave speed, $T$ tension, $\sigma$ surface density.

```python
import numpy as np

# Theory: f_mn = f_11 * sqrt(m^2 + n^2) / sqrt(2)
f11 = 180  # Hz, measure this first

modes = []
for m in range(1, 6):
    for n in range(1, 6):
        f = f11 * np.sqrt(m**2 + n**2) / np.sqrt(2)
        modes.append((m, n, f))

modes.sort(key=lambda x: x[2])
for m, n, f in modes[:12]:
    print(f"({m},{n}): {f:.1f} Hz")
```

**Arduino sweep code:**
```cpp
// Sweep 50Hz to 2000Hz, log spacing, 2 second sweep
void loop() {
  for (float f = 50; f < 2000; f *= 1.01) {
    tone(9, (int)f);
    delay(20);
    int adc = analogRead(A0);
    Serial.print(f); Serial.print(","); Serial.println(adc);
  }
  noTone(9);
  delay(2000);
}
```

3. Log serial output to Python, plot amplitude vs frequency. Identify peaks.
4. Compare measured $f_{mn}$ ratios to theoretical $\sqrt{m^2+n^2}/\sqrt{2}$.

```python
import serial
import numpy as np
import matplotlib.pyplot as plt

# Read serial data
freqs, amps = [], []
with serial.Serial('/dev/ttyUSB0', 9600, timeout=10) as ser:
    for _ in range(500):
        line = ser.readline().decode().strip()
        f, a = map(float, line.split(','))
        freqs.append(f); amps.append(a)

plt.semilogy(freqs, amps, color='gold')
plt.xlabel('Frequency (Hz)'); plt.ylabel('Amplitude')
plt.title('Membrane Resonance Spectrum')
plt.grid(True, alpha=0.3); plt.show()
```

**Fault finding:**
- Peaks not visible → increase speaker power; check microphone gain; try placing mic closer to membrane
- Too many peaks → standing waves in the room; mount speaker on membrane edge not frame; damp the wooden frame
- Peaks don't match ratios → membrane not uniformly tensioned; re-stretch and re-staple

#### Stage 3 — Visualise mode shapes (Chladni figures)

1. Scatter a thin layer of cornstarch (or fine sand) on horizontal membrane.
2. Drive at each resonant frequency $f_{mn}$.
3. Photograph the nodal pattern — cornstarch collects at nodes.
4. Compare observed nodal lines to the theoretical eigenfunctions $\psi_{mn}(x,y) = \sin(m\pi x/a)\sin(n\pi y/b)$.

The nodal patterns **are** the wavefunctions $\Psi_n[g_{ij}]$ of the Wheeler-DeWitt equation — each standing wave pattern is a quantum state of the 2D universe.

#### Stage 4 — Change modulus $\tau$ (aspect ratio)

1. Build a second frame with aspect ratio $b/a = 1.5$ (i.e. $\tau = 1.5i$).
2. Repeat the frequency sweep. The new mode frequencies:

$$f_{mn} = \frac{c}{2}\sqrt{\frac{m^2}{a^2} + \frac{n^2}{b^2}}$$

3. Plot the spectrum change. The mode ordering changes as $\tau$ varies — some modes become degenerate, others split. This is exactly the modular structure of the 2+1D gravity path integral.

```python
def torus_modes(tau2, a=0.2, c=200, n_modes=10):
    b = a * tau2
    modes = []
    for m in range(1, 8):
        for n in range(1, 8):
            f = (c/2) * np.sqrt((m/a)**2 + (n/b)**2)
            modes.append(f)
    return sorted(modes)[:n_modes]

for tau2 in [1.0, 1.2, 1.5, 2.0]:
    modes = torus_modes(tau2)
    print(f"τ={tau2:.1f}: {[f'{f:.0f}' for f in modes[:6]]} Hz")
```

**Pass criterion:** measured frequency ratios match theory within 5% for at least the first 6 modes.

---

<a name="exp4"></a>
## Experiment 4 — Cauchy Horizon / Acoustic Black Hole

### What this tests

In 2+1D AdS gravity, the Cauchy horizon separates causal regions. The analogue in flowing fluids is the **acoustic horizon** (dumb hole) — where flow velocity $v$ exceeds local sound/wave speed $c_s$. The metric of a 1+1D acoustic black hole:

$$ds^2 = -(c_s^2 - v^2)dt^2 - 2v\, dx\, dt + dx^2$$

This is formally identical to the Painlevé-Gullstrand metric for a black hole. The horizon sits at $x_h$ where $v(x_h) = c_s$.

**Hawking radiation analogue:** quantum vacuum fluctuations at the acoustic horizon produce correlated phonon pairs — one falls "in", one escapes. The analogue Hawking temperature:

$$T_H = \frac{\hbar}{2\pi k_B} \left.\frac{d(v - c_s)}{dx}\right|_{x=x_h}$$

For surface waves on water: $c_s = \sqrt{gh}$ (shallow water) or $c_s = \sqrt{g\lambda/2\pi}$ (deep water).

---

### What to Build

A flow channel with a constriction (weir or step) that accelerates water through the wave speed.

**Parts list:**

| Item | Spec | Approx cost |
|---|---|---|
| Clear acrylic tank | 600×100×100mm | $20 |
| Small water pump | 240L/hr, 5W | $8 |
| Rubber tubing | 12mm ID | $5 |
| Food colouring | Visualisation | $2 |
| Plastic weir insert | 3D print or cut acrylic | $5 |
| High-speed camera / phone | 240fps min | owned |

**Total: ~$40**

---

### Build Steps

#### Stage 1 — Establish flow

1. Set up recirculating flow: pump → tank inlet → weir → outlet → pump.
2. Measure flow rate $Q$ by timing how long to fill 1L.
3. Measure channel width $w$ and upstream depth $h$.
4. Flow velocity: $v = Q / (w \cdot h)$.
5. Wave speed in shallow water: $c_s = \sqrt{gh}$.
6. Froude number: $Fr = v/c_s$. Need $Fr > 1$ downstream of weir.

**Target geometry:** upstream depth $h_1 = 40\text{mm}$, downstream $h_2 = 8\text{mm}$. Weir drop creates acceleration.

```python
import numpy as np

g = 9.81
Q = 0.0002  # m³/s (200 mL/s — measure this)
w = 0.08    # channel width, 80mm

h_upstream = 0.040    # 40mm
h_downstream = 0.008  # 8mm

v_up = Q / (w * h_upstream)
v_down = Q / (w * h_downstream)
cs_up = np.sqrt(g * h_upstream)
cs_down = np.sqrt(g * h_downstream)

Fr_up = v_up / cs_up
Fr_down = v_down / cs_down

print(f"Upstream: v={v_up:.3f} m/s, cs={cs_up:.3f} m/s, Fr={Fr_up:.2f}")
print(f"Downstream: v={v_down:.3f} m/s, cs={cs_down:.3f} m/s, Fr={Fr_down:.2f}")
print(f"Horizon exists: {Fr_up < 1 and Fr_down > 1}")
```

**Fault finding:**
- $Fr < 1$ everywhere → increase pump flow rate or reduce downstream depth further
- Turbulent flow → reduce pump speed; add a flow straightener (sponge) at inlet
- Can't see waves → add a drop of food colouring upstream; use bright side lighting

#### Stage 2 — Generate and observe wave blocking

1. Dip a stick briefly into the upstream region to create circular ripples.
2. Watch the ripples propagate: **upstream** they travel freely; **downstream past the horizon** they are swept away and cannot propagate back upstream.
3. Film at 240fps. Extract frame-by-frame wave positions.

This **wave blocking** is the classical analogue of the event horizon. Waves = light; horizon = where $v = c_s$.

#### Stage 3 — Measure horizon location

1. Introduce a small continuous wave source (vibrating wire touching water at 10Hz) upstream.
2. Photograph or film the standing wave pattern. The horizon is the point where the upstream-propagating wave has zero net velocity — a standing wave front frozen at $x_h$.

```python
# From video: extract wave crest positions vs time
# horizon is where wave speed = flow speed → node that doesn't move

# Measure crest positions [x1, x2, x3...] at times [t1, t2, t3...]
# Phase velocity = dx/dt for each crest
# Plot phase velocity vs x — horizon is where it crosses zero
```

#### Stage 4 — Analogue Hawking temperature

In the experiment by Weinfurtner et al. (2011), correlated wave pairs are detected at the horizon. To measure this requires:

1. Two simultaneous wave probes (thin wires + ADC) placed equidistant from horizon on each side.
2. Record wave amplitude vs time at both probes simultaneously.
3. Compute cross-correlation:

$$C(\Delta t) = \langle \eta_1(t) \eta_2(t + \Delta t) \rangle$$

A positive peak in $C$ at finite $\Delta t$ indicates correlated pairs — the analogue Hawking signal.

**Pass criterion for Stage 2:** clear wave blocking visible in video. The horizon location is reproducible to within ±5mm between runs.

---

<a name="exp5"></a>
## Experiment 5 — Wheeler-DeWitt Tunnelling / LC Circuit

### What this tests

The Wheeler-DeWitt equation in 2+1D mini-superspace (single toroidal universe, $\Lambda > 0$):

$$\left[-\frac{\partial^2}{\partial V^2} + \Lambda V^{2/3} - E\right]\Psi(V) = 0$$

where $V$ is the spatial volume of the universe. For small $V$ (near the big bang), this is a **quantum tunnelling problem** — the universe nucleates by tunnelling through a potential barrier from $V=0$.

The potential barrier is:

$$U(V) = \Lambda V^{2/3} - \frac{1}{V^{4/3}}$$

(the second term is the spatial curvature contribution). The tunnelling amplitude is:

$$\Psi \sim \exp\left(-\int_{V_1}^{V_2} \sqrt{U(V) - E}\, dV\right)$$

This is **identical** to quantum tunnelling through a 1D potential barrier in standard QM — which is exactly what a tunnel diode does.

The LC circuit analogue maps:
- Volume $V$ → charge $Q$ on capacitor
- Potential $U(V)$ → effective potential in LC oscillator
- Planck constant $\hbar$ → noise floor / thermal fluctuations

---

### What to Build — Option A: Tunnel Diode Oscillator

**Parts list:**

| Item | Spec | Approx cost |
|---|---|---|
| Tunnel diode | 1N3717 or BD3 or similar | $5 |
| Inductor | 100µH | $1 |
| Capacitor | 100pF, 1nF, 10nF (×3) | $1 |
| Variable resistor | 10kΩ pot | $1 |
| 9V battery + clip | Power | $2 |
| Oscilloscope | — | owned |

**Total: ~$10**

---

### Build Steps — Option A

#### Stage 1 — Characterise the tunnel diode I-V curve

The tunnel diode has a **negative differential resistance** (NDR) region — as voltage increases, current *decreases*. This is the direct analogue of the inverted potential barrier the WdW wavefunction tunnels through.

```
        I
        |    peak
        |   /\
        |  /  \
        | /    \_____ valley
        |/
        +----------------> V
           Vp   Vv
```

1. Wire tunnel diode in series with a 100Ω resistor and a variable voltage source (pot from 0-1V).
2. Measure $V_D$ and $I_D = V_R/100\Omega$ at each pot setting.
3. Plot I-V curve. Identify peak voltage $V_p$, valley voltage $V_v$, peak current $I_p$.

```python
V_diode = np.array([...])  # measured
I_diode = np.array([...])  # measured

plt.figure(figsize=(8,5))
plt.plot(V_diode * 1000, I_diode * 1000, color='gold', lw=2)
plt.xlabel('Voltage (mV)'); plt.ylabel('Current (mA)')
plt.title('Tunnel Diode I-V Characteristic')

# Mark NDR region
plt.axvspan(V_peak*1000, V_valley*1000, alpha=0.2, color='red',
            label='NDR region (tunnelling)')
plt.legend(); plt.grid(True, alpha=0.3); plt.show()
```

**Fault finding:**
- No NDR region visible → diode may be dead; try forward-biasing only from 0 to 200mV in 5mV steps
- NDR very flat → component heating; take measurements quickly; cool component between readings
- Oscillations on I-V trace → circuit is already oscillating; add 10Ω series resistor to stabilise

#### Stage 2 — Build the oscillator

Connect: battery → 10kΩ pot (bias) → 100µH inductor → tunnel diode → GND

The LC tank circuit resonates at:

$$f_0 = \frac{1}{2\pi\sqrt{LC}}$$

With $L = 100\mu\text{H}$, $C = 100\text{pF}$: $f_0 = \frac{1}{2\pi\sqrt{10^{-4} \times 10^{-10}}} \approx 1.59\text{ MHz}$

1. Adjust the bias pot slowly from 0V upward.
2. At the bias point in the NDR region, the circuit will spontaneously begin oscillating.
3. Monitor with oscilloscope — you should see a sinusoidal output growing from noise.

**The onset of oscillation is the analogue of quantum nucleation** — the system tunnels from the "no oscillation" ground state to the oscillating state, just as the WdW universe tunnels from $V=0$.

#### Stage 3 — Measure nucleation statistics

1. Set bias to just below the NDR threshold.
2. Record the time from power-on to onset of oscillation across 20 trials.
3. The distribution of nucleation times should be **exponential** — $P(t) = \lambda e^{-\lambda t}$.

```python
# Nucleation times from oscilloscope measurements
t_nucleate = np.array([...])  # seconds

# Fit exponential
from scipy.stats import expon
loc, scale = expon.fit(t_nucleate, floc=0)
lambda_fit = 1/scale

t_range = np.linspace(0, max(t_nucleate), 200)
plt.figure(figsize=(8,5))
plt.hist(t_nucleate, bins=10, density=True, color='gold', alpha=0.7, label='Data')
plt.plot(t_range, expon.pdf(t_range, scale=scale), color='orange',
         lw=2, label=f'Exp fit λ={lambda_fit:.2f} s⁻¹')
plt.xlabel('Nucleation time (s)'); plt.ylabel('Probability density')
plt.title('WdW Tunnelling Analogue: Nucleation Statistics')
plt.legend(); plt.grid(True, alpha=0.3); plt.show()

print(f"Mean nucleation time: {np.mean(t_nucleate):.3f} s")
print(f"Tunnelling rate: {lambda_fit:.3f} s⁻¹")
```

4. Change the bias resistor value (changes effective barrier height) and repeat. The tunnelling rate should follow:

$$\lambda \propto \exp\left(-2\int_{Q_1}^{Q_2}\sqrt{2m(U(Q)-E)}\,dQ\right)$$

**Pass criterion:** nucleation time distribution passes a KS test for exponentiality ($p > 0.05$); tunnelling rate increases monotonically as bias enters deeper into NDR region.

**Fault finding:**
- Oscillator doesn't start → bias not in NDR region; check I-V curve and adjust pot
- Oscillates continuously → bias too deep in NDR; pull back toward NDR edge
- Nucleation too fast to measure → add series resistance to slow the dynamics
- Nucleation deterministic (same time every trial) → system is not noise-driven; reduce supply decoupling, allow more thermal noise

---

### What to Build — Option B: Numerical WdW Simulation

If the tunnel diode is unavailable, simulate directly.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import solve_ivp

# WdW equation: -d²Ψ/dV² + U(V)Ψ = EΨ
# U(V) = Lambda * V^(2/3) - V^(-4/3)

Lambda = 1.0

def U(V):
    V = np.maximum(V, 1e-6)
    return Lambda * V**(2/3) - V**(-4/3)

def wdw_rhs(V, y, E):
    psi, dpsi = y
    d2psi = (U(V) - E) * psi
    return [dpsi, d2psi]

V_span = (0.01, 6.0)
V_eval = np.linspace(*V_span, 2000)

fig, axes = plt.subplots(2, 2, figsize=(12, 9), facecolor='#0a0a0a')
fig.suptitle('Wheeler-DeWitt Tunnelling — Numerical Solutions', color='gold',
             fontsize=14)

for ax, E in zip(axes.flat, [-0.5, 0.0, 0.5, 1.0]):
    ax.set_facecolor('#0a0a0a')

    # Potential
    V_plot = np.linspace(0.05, 6, 500)
    ax.plot(V_plot, U(V_plot), color='#FF8C00', lw=1.5, label='U(V)', ls='--')
    ax.axhline(E, color='#FFA500', lw=1, ls=':', alpha=0.7, label=f'E={E}')

    # Solve WdW
    try:
        sol = solve_ivp(wdw_rhs, V_span, [0.0, 0.1], args=(E,),
                        t_eval=V_eval, max_step=0.01, method='RK45')
        psi = sol.y[0]
        psi /= np.max(np.abs(psi))  # normalise
        ax.plot(V_eval, psi, color='#FFD700', lw=2, label='Ψ(V)')
    except Exception as e:
        ax.text(3, 0, str(e), color='red')

    # Mark classical turning points
    turning = V_plot[np.diff(np.sign(U(V_plot) - E)) != 0]
    for vt in turning:
        ax.axvline(vt, color='white', lw=0.8, alpha=0.5)

    ax.set_xlim(0, 6); ax.set_ylim(-3, 5)
    ax.set_xlabel('Volume V', color='white')
    ax.set_ylabel('Ψ(V) / U(V)', color='white')
    ax.set_title(f'E = {E}', color='gold')
    ax.tick_params(colors='white')
    ax.legend(fontsize=8)
    ax.grid(True, alpha=0.2)

plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/wdw_tunnelling.png',
            dpi=150, bbox_inches='tight', facecolor='#0a0a0a')
plt.show()
```

---

## Master Fault-Finding Reference

| Symptom | Likely cause | Fix |
|---|---|---|
| Berry phase wrong sign | Fibre coil wound wrong direction | Reverse coil winding |
| SSH edge mode missing | $t_2/t_1$ ratio too small | Use $C_2/C_1 \geq 5$ |
| Membrane peaks broadened | Damping too high | Use thinner latex; reduce air resistance |
| No acoustic horizon | Flow rate insufficient | Increase pump; reduce downstream depth |
| Tunnel diode won't oscillate | Wrong bias point | Use I-V curve; bias exactly at NDR midpoint |
| Frequencies all off by constant factor | Temperature changed wave speed | Re-calibrate $c_s = \sqrt{gh}$ at start of each session |

---

## Measurement Log Template

```
Experiment: ___________   Date: ___________   Operator: ___________

Stage completed: [ ] 1  [ ] 2  [ ] 3  [ ] 4

Key measurements:
  Parameter 1: ________ (expected: ________)
  Parameter 2: ________ (expected: ________)

Theory vs measurement:
  Quantity       Theory    Measured   % error
  __________     ______    ________   _______
  __________     ______    ________   _______

Pass/Fail: _____   Notes: ___________________________
```

---

## Recommended Build Order

| Order | Experiment | Why |
|---|---|---|
| 1st | Exp 5B — Numerical WdW | No hardware; verify understanding first |
| 2nd | Exp 3 — Membrane modes | Cheapest hardware; immediate visual results |
| 3rd | Exp 5A — Tunnel diode | Direct tunnelling analogue; fast to build |
| 4th | Exp 4 — Acoustic horizon | Satisfying visual; needs pump setup |
| 5th | Exp 1 — Fibre Berry phase | Most relevant to memR; requires optical care |
| 6th | Exp 2 — SSH chain | Most subtle; requires LCR precision |

---

## Key References

- Carlip, S. (2005). *Quantum Gravity in 2+1 Dimensions: The Case of a Closed Universe.* Living Reviews in Relativity.
- Berry, M.V. (1984). *Quantal phase factors accompanying adiabatic changes.* Proc. R. Soc. A.
- Weinfurtner, S. et al. (2011). *Measurement of stimulated Hawking emission in an analogue system.* PRL 106.
- Su, W.P., Schrieffer, J.R., Heeger, A.J. (1979). *Solitons in polyacetylene.* PRL 42.
- Unruh, W.G. (1981). *Experimental black-hole evaporation?* PRL 46.
- Swingle, B. (2012). *Entanglement renormalization and holography.* PRD 86.

---

*Generated: 2026-07-04*
