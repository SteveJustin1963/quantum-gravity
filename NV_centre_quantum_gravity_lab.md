# NV Centre Quantum Gravity Detector — Full Lab Protocol

Based on: Marshman et al., *New J. Phys.* 22, 083012 (2020)  
and Bose et al., *Phys. Rev. Lett.* 119, 240401 (2017)

**Goal:** Build a table-top quantum superposition gravitational sensor using nitrogen-vacancy (NV) centres in diamond. Test whether gravity is a quantum phenomenon.

---

## Phased Roadmap

| Phase | What | Achievable | Cost |
|---|---|---|---|
| 0 | QuTiP simulation — full spin dynamics | Now, any PC | $0 |
| 1 | NV spin superposition + Ramsey spectroscopy | Now, quantum optics lab | ~$5k–$20k |
| 2 | Spin-spatial superposition via gradient | Frontier research | ~$50k+ |
| 3 | Environmental noise reduction | Engineering challenge | ~$200k+ |
| 4 | Two-system gravity entanglement | Not yet achieved | Multi-$M |
| 5 | Free-fall drop tower | Decades | Facility-scale |

**Start here:** Phase 0 (this week, your laptop). Phase 1 requires lab access but is real physics today.

---

## Table of Contents

1. [Physics and Mathematics](#physics)
2. [Phase 0 — QuTiP Simulation](#phase0)
3. [Phase 1 — NV Spin Superposition (Hardware)](#phase1)
4. [Phase 2 — Spin-Spatial Superposition](#phase2)
5. [Phase 3 — Noise Reduction](#phase3)
6. [Phase 4 — Gravity Entanglement Test](#phase4)
7. [Master Fault-Finding Reference](#faults)
8. [Safety](#safety)
9. [References](#refs)

---

<a name="physics"></a>
## 1. Physics and Mathematics

### 1.1 The Nitrogen-Vacancy Centre

A nitrogen-vacancy (NV) centre is a point defect in diamond: one carbon atom replaced by nitrogen, with a vacancy (missing atom) adjacent. It traps an extra electron, giving a spin-1 system.

The ground-state Hamiltonian (zero field):

$$H_0 = D S_z^2$$

where $D = 2.87\,\text{GHz}$ is the zero-field splitting and $S_z$ is the spin-1 operator. The three ground states are $|m_s = 0\rangle$, $|m_s = +1\rangle$, $|m_s = -1\rangle$.

With an applied magnetic field $B$ along the NV axis:

$$H = D S_z^2 + \gamma_e B S_z$$

where $\gamma_e = 28.0\,\text{GHz/T}$ is the electron gyromagnetic ratio. The transition frequencies:

$$f_{\pm} = D \pm \gamma_e B$$

At $B = 0$: both $|0\rangle \to |\pm1\rangle$ transitions sit at 2.87 GHz.

The spin-1 operators:

$$S_z = \begin{pmatrix}1&0&0\\0&0&0\\0&0&-1\end{pmatrix}, \quad
S_x = \frac{1}{\sqrt{2}}\begin{pmatrix}0&1&0\\1&0&1\\0&1&0\end{pmatrix}, \quad
S_y = \frac{1}{\sqrt{2}}\begin{pmatrix}0&-i&0\\i&0&-i\\0&i&0\end{pmatrix}$$

---

### 1.2 Optically Detected Magnetic Resonance (ODMR)

NV centres are initialised and read out optically:

- **532 nm laser** pumps the NV into $|m_s = 0\rangle$ via spin-selective intersystem crossing
- **Fluorescence** at 637–800 nm: $|0\rangle$ emits ~30% more photons than $|±1\rangle$
- **Contrast** (fractional change): $C = (F_0 - F_{\pm1})/F_0 \approx 0.2$–$0.3$

The fluorescence rate:

$$F(t) = F_0\left[1 - C \cdot P_{|\pm1\rangle}(t)\right]$$

where $P_{|\pm1\rangle}$ is the population in $|±1\rangle$.

---

### 1.3 Ramsey Interferometry

The Ramsey sequence is a two-pulse interferometer on the spin:

```
|Init 532nm| → |π/2 MW| → |free evolve τ| → |π/2 MW| → |Readout 532nm|
```

**Step by step:**

1. Initialise: $|\psi_0\rangle = |0\rangle$

2. $\pi/2$ pulse (rotation about $x$):
$$|\psi_1\rangle = R_x(\pi/2)|0\rangle = \frac{1}{\sqrt{2}}(|0\rangle - i|+1\rangle)$$

3. Free evolution for time $\tau$ under $H = D S_z^2 + \gamma_e B S_z$:
$$|\psi(\tau)\rangle = \frac{1}{\sqrt{2}}\left(|0\rangle - i e^{-i\omega_{01}\tau}|+1\rangle\right)$$

where $\omega_{01} = 2\pi(D + \gamma_e B)$.

4. Second $\pi/2$ pulse and readout. The fluorescence signal:

$$F(\tau) = F_0\left[1 - \frac{C}{2}\left(1 + \cos(\omega_{01}\tau + \phi)\right)\right]$$

The oscillation frequency $\omega_{01}$ measures the local magnetic field. **Any phase shift $\Delta\phi$ encodes a change in the environment** — including gravitational acceleration.

---

### 1.4 Spin Echo (Hahn Echo)

To extend coherence, insert a $\pi$ pulse at $\tau/2$:

```
|π/2| → |τ/2| → |π| → |τ/2| → |π/2| → readout
```

The $\pi$ pulse refocuses quasi-static noise. The coherence time extends from $T_2^*$ (Ramsey, limited by inhomogeneous broadening) to $T_2$ (echo, limited by dynamical noise):

$$T_2^* \sim 1\,\mu\text{s} \quad \to \quad T_2 \sim 1\,\text{ms}$$

For dynamical decoupling with $n$ pulses (XY-8 sequence), coherence extends further:

$$T_2(n) \approx T_2^{(0)} \cdot n^{2/3}$$

---

### 1.5 Spin-Spatial Superposition via Stern-Gerlach

Apply a magnetic field gradient $\partial B/\partial z$ along the NV axis. The force on the diamond:

$$F_{m_s} = m_s \cdot \mu_B \cdot g_e \cdot \frac{\partial B}{\partial z}$$

where $\mu_B = 9.274 \times 10^{-24}\,\text{J/T}$ and $g_e \approx 2$.

For the spin superposition $(|0\rangle + |+1\rangle)/\sqrt{2}$:
- $|0\rangle$ component: zero force
- $|+1\rangle$ component: force $F = \mu_B g_e \partial_z B$

After time $t$, the spatial separation:

$$\Delta z(t) = \frac{F}{2m} t^2 = \frac{\mu_B g_e \partial_z B}{2m} t^2$$

For diamond ($m = 10^{-17}\,\text{kg}$), gradient $\partial_z B = 10^6\,\text{T/m}$, $t = 1\,\text{ms}$:

$$\Delta z = \frac{(9.274 \times 10^{-24})(2)(10^6)}{2 \times 10^{-17}}(10^{-3})^2 \approx 9.3 \times 10^{-7}\,\text{m} \approx 1\,\mu\text{m}$$

This is the **spatial superposition** size — macroscopic enough to be interesting.

---

### 1.6 Phase Accumulated by Gravity

If the two arms of the superposition experience a differential acceleration $\Delta a$ (due to a gravitational wave or gradient), the relative phase accumulated:

$$\Delta\phi = \frac{m \Delta a \cdot \Delta z \cdot T^2}{\hbar}$$

For $\Delta a = 10^{-15}\,\text{m/s}^2$ (gravitational wave strain at relevant frequency), $\Delta z = 1\,\mu\text{m}$, $T = 1\,\text{ms}$:

$$\Delta\phi \approx \frac{(10^{-17})(10^{-15})(10^{-6})(10^{-6})}{10^{-34}} \approx 10^{-10}\,\text{rad}$$

This is extremely small — why we need very long coherence times and large $\Delta z$.

---

### 1.7 Gravity-Induced Entanglement (Phase 4)

For two diamonds $A$ and $B$ separated by distance $r$, each in spatial superposition $\Delta z$, the gravitational interaction energy between the four mass configurations:

$$U_{ij} = -\frac{Gm^2}{r_{ij}}, \quad i,j \in \{0,1\}$$

The relative phase accumulated between the $|00\rangle$ and $|11\rangle$ configurations:

$$\Delta\phi_{grav} = \frac{Gm^2}{\hbar}\left(\frac{1}{r} - \frac{1}{r+\Delta z}\right)T \approx \frac{Gm^2 \Delta z}{\hbar r^2} T$$

For $m = 10^{-17}\,\text{kg}$, $\Delta z = 250\,\mu\text{m}$, $r = 200\,\mu\text{m}$, $T = 1\,\text{s}$:

$$\Delta\phi_{grav} \approx \frac{(6.67\times10^{-11})(10^{-17})^2(250\times10^{-6})}{(10^{-34})(200\times10^{-6})^2 (1)} \approx 2.6 \times 10^{-3}\,\text{rad}$$

To witness entanglement, need $\Delta\phi_{grav} > 1/\sqrt{N_{trials}}$ — requiring either more trials or longer coherence time.

The entanglement is confirmed if the CHSH inequality is violated:

$$|\langle AB\rangle + \langle AB'\rangle + \langle A'B\rangle - \langle A'B'\rangle| > 2$$

where $A, A', B, B'$ are spin measurements on the two diamonds in different bases.

---

<a name="phase0"></a>
## 2. Phase 0 — QuTiP Simulation

### Install dependencies

```bash
pip install qutip numpy scipy matplotlib
```

### 2.1 NV Hamiltonian and ODMR Spectrum

```python
import numpy as np
import matplotlib.pyplot as plt
import qutip as qt

# NV spin-1 operators
Sz = qt.jmat(1, 'z')
Sx = qt.jmat(1, 'x')
Sy = qt.jmat(1, 'y')

# Parameters
D = 2.87e9          # Hz, zero-field splitting
gamma_e = 28.0e9    # Hz/T, electron gyromagnetic ratio
B = 0.0             # T, static field along NV axis

# Ground state Hamiltonian
H0 = D * Sz * Sz + gamma_e * B * Sz

# Eigenvalues and eigenvectors
evals, evecs = H0.eigenstates()
print("Eigenvalues (GHz):", [f"{e/1e9:.4f}" for e in evals])
print("Transition |0>->|+1> (GHz):", (evals[2]-evals[1])/1e9)
print("Transition |0>->|-1> (GHz):", (evals[1]-evals[0])/1e9)

# Plot ODMR spectrum (CW microwave sweep)
f_range = np.linspace(2.5e9, 3.2e9, 500)
B_vals  = np.linspace(0, 5e-3, 4)   # 0 to 5 mT

fig, ax = plt.subplots(figsize=(10, 6), facecolor='#0a0a0a')
ax.set_facecolor('#0a0a0a')

colors = ['#FFD700', '#FFA500', '#FF8C00', '#FF4500']
for B_val, color in zip(B_vals, colors):
    H_B = D * Sz * Sz + gamma_e * B_val * Sz
    evals_B = H_B.eigenenergies()
    f_plus  = (evals_B[2] - evals_B[1]) / (2 * np.pi)
    f_minus = (evals_B[1] - evals_B[0]) / (2 * np.pi)

    # Lorentzian linewidth ~1 MHz
    lw = 1e6
    odmr = 1.0 - 0.15*(lw**2/((f_range-f_plus)**2+lw**2)) \
               - 0.15*(lw**2/((f_range-f_minus)**2+lw**2))
    ax.plot(f_range/1e9, odmr, color=color, lw=1.5,
            label=f'B={B_val*1e3:.1f} mT')

ax.set_xlabel('Microwave Frequency (GHz)', color='white')
ax.set_ylabel('Normalised Fluorescence', color='white')
ax.set_title('NV Centre ODMR Spectrum', color='#FFD700')
ax.tick_params(colors='white')
ax.legend(fontsize=9)
ax.grid(True, alpha=0.2)
plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/odmr_spectrum.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()
```

**Expected output:** Two dips symmetric about 2.87 GHz, splitting linearly with $B$.

**Fault finding:**
- Both dips at same frequency → B=0 is correct; add a small B to split them
- Dips merge or overlap → linewidth too large; reduce `lw` to 0.5e6

---

### 2.2 Ramsey Sequence Simulation

```python
import numpy as np
import matplotlib.pyplot as plt
import qutip as qt
from qutip import mesolve, Options

Sz = qt.jmat(1, 'z')
Sx = qt.jmat(1, 'x')
Sy = qt.jmat(1, 'y')
I3 = qt.qeye(3)

D       = 2.87e9    # Hz
gamma_e = 28.0e9    # Hz/T
B       = 1e-3      # T

omega_01 = 2 * np.pi * (D + gamma_e * B)  # |0> -> |+1> transition

# Resonant microwave drive
omega_mw = omega_01
H_drive_coeff = '0.5 * Omega * cos(omega_mw * t)'

# Rabi frequency
Omega = 2 * np.pi * 10e6   # 10 MHz Rabi frequency → π/2 pulse time = 12.5 ns

# Lindblad collapse operators
T1  = 1e-3   # s, longitudinal relaxation
T2s = 2e-6   # s, T2* dephasing

c_ops = [
    np.sqrt(1/T1) * Sz,           # T1 relaxation
    np.sqrt(1/T2s) * Sz * Sz,     # pure dephasing
]

# Initial state: |ms=0> (bright state)
# In the {-1, 0, +1} ordering from qutip.jmat, |0> is index 1
psi0 = qt.basis(3, 1)

# ── π/2 pulse ────────────────────────────────────────────────────────────────
t_pi2 = np.pi / (2 * Omega)   # π/2 pulse duration
H_pi2 = [D * Sz * Sz + gamma_e * B * Sz,
         [Sx, f'{Omega} * np.cos({omega_mw} * t)']]

def run_ramsey(tau_vals, T2star=2e-6):
    signal = []
    for tau in tau_vals:
        # Simplified: evolve analytically for speed
        # State after π/2: (|0> - i|+1>)/sqrt(2)
        # After free evolution: phase phi = omega_01 * tau
        # After second π/2: P(0) = cos^2(omega_01*tau/2)
        phi = omega_01 * tau
        # T2* decay envelope
        decay = np.exp(-tau / T2star)
        P0 = 0.5 * (1 + np.cos(phi) * decay)
        # Fluorescence: bright when in |0>
        F = 1.0 - 0.3 * (1 - P0)
        signal.append(F)
    return np.array(signal)

tau_vals = np.linspace(0, 10e-6, 2000)   # 0 to 10 µs
signal   = run_ramsey(tau_vals)

fig, axes = plt.subplots(2, 1, figsize=(12, 8), facecolor='#0a0a0a')

# Time domain
ax = axes[0]
ax.set_facecolor('#0a0a0a')
ax.plot(tau_vals * 1e6, signal, color='#FFD700', lw=1.5)
ax.set_xlabel('Free evolution time τ (µs)', color='white')
ax.set_ylabel('Fluorescence (norm.)', color='white')
ax.set_title('Ramsey Fringes — NV Centre', color='#FFD700')
ax.tick_params(colors='white')
ax.grid(True, alpha=0.2)

# Frequency domain (FFT)
ax2 = axes[1]
ax2.set_facecolor('#0a0a0a')
dt = tau_vals[1] - tau_vals[0]
freq = np.fft.rfftfreq(len(signal), dt)
spectrum = np.abs(np.fft.rfft(signal - signal.mean()))
ax2.plot(freq / 1e6, spectrum, color='#FFA500', lw=1.5)
ax2.set_xlabel('Frequency (MHz)', color='white')
ax2.set_ylabel('Spectral amplitude', color='white')
ax2.set_title('FFT of Ramsey Signal — peak = detuning from resonance', color='#FFD700')
ax2.set_xlim(0, 100)
ax2.tick_params(colors='white')
ax2.grid(True, alpha=0.2)

plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/ramsey_simulation.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()

# Extract T2*
from scipy.optimize import curve_fit

def ramsey_model(tau, A, T2s, omega, phi, offset):
    return A * np.exp(-tau / T2s) * np.cos(omega * tau + phi) + offset

p0 = [0.15, 2e-6, omega_01, 0, 0.85]
try:
    popt, pcov = curve_fit(ramsey_model, tau_vals, signal, p0=p0,
                           maxfev=10000)
    T2s_fit = popt[1]
    print(f"Fitted T2* = {T2s_fit*1e6:.2f} µs")
    print(f"Fitted frequency = {popt[2]/(2*np.pi)/1e9:.6f} GHz")
except Exception as e:
    print(f"Fit failed: {e}")
```

**Expected output:** Decaying cosine oscillation. FFT peak at the detuning frequency.

**Fault finding:**
- No oscillation → `omega_01` and `omega_mw` equal (zero detuning); add a small detuning `omega_mw = omega_01 * 1.001`
- Oscillation doesn't decay → T2star too large; reduce to 1e-7
- FFT peak at wrong frequency → check unit consistency (Hz vs rad/s)

---

### 2.3 Spin Echo (Hahn Echo) — Extending Coherence

```python
def run_echo(tau_vals, T2=1e-3, noise_bandwidth=1e3):
    signal = []
    for tau in tau_vals:
        # Hahn echo refocuses quasi-static noise
        # Residual decay from dynamical noise only
        decay = np.exp(-(tau / T2)**3)   # stretched exponential for 1/f noise
        phi = omega_01 * tau
        # Echo signal: no static detuning term (refocused)
        # Only dynamic noise remains
        F = 0.5 * (1 + decay)
        signal.append(F)
    return np.array(signal)

tau_echo = np.linspace(0, 2e-3, 1000)
signal_ramsey = run_ramsey(tau_echo, T2star=2e-6)
signal_echo   = run_echo(tau_echo, T2=1e-3)

fig, ax = plt.subplots(figsize=(12, 5), facecolor='#0a0a0a')
ax.set_facecolor('#0a0a0a')
ax.plot(tau_echo * 1e3, signal_ramsey, color='#FFA500', lw=1.5,
        label=f'Ramsey (T2*=2µs)', alpha=0.7)
ax.plot(tau_echo * 1e3, signal_echo, color='#FFD700', lw=2,
        label=f'Hahn Echo (T2=1ms)')
ax.set_xlabel('Total evolution time (ms)', color='white')
ax.set_ylabel('Fluorescence (norm.)', color='white')
ax.set_title('Ramsey vs Spin Echo — Coherence Extension', color='#FFD700')
ax.tick_params(colors='white')
ax.legend(fontsize=10)
ax.grid(True, alpha=0.2)
plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/echo_coherence.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()
```

---

### 2.4 Spatial Superposition Size vs. Time

```python
import numpy as np
import matplotlib.pyplot as plt

# Physical parameters
mu_B   = 9.274e-24   # J/T
g_e    = 2.0
m_diam = 1e-17       # kg
hbar   = 1.055e-34   # J·s

grad_B_vals = [1e4, 1e5, 1e6, 1e7]  # T/m
t_vals = np.linspace(0, 1e-3, 500)  # 0 to 1 ms

fig, axes = plt.subplots(1, 2, figsize=(14, 6), facecolor='#0a0a0a')
colors = ['#FFD700', '#FFA500', '#FF8C00', '#FF4500']

for ax, unit, scale, ylabel in zip(
        axes, ['nm', 'µm'], [1e9, 1e6],
        ['Spatial separation Δz (nm)', 'Spatial separation Δz (µm)']):
    ax.set_facecolor('#0a0a0a')
    for dBdz, color in zip(grad_B_vals, colors):
        F = mu_B * g_e * dBdz
        delta_z = F / (2 * m_diam) * t_vals**2
        ax.plot(t_vals * 1e3, delta_z * scale, color=color, lw=2,
                label=f'∂B/∂z = {dBdz:.0e} T/m')
    ax.set_xlabel('Time (ms)', color='white')
    ax.set_ylabel(ylabel, color='white')
    ax.set_title('Spatial Superposition Size', color='#FFD700')
    ax.tick_params(colors='white')
    ax.legend(fontsize=9)
    ax.grid(True, alpha=0.2)

plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/spatial_superposition.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()

# Print key numbers
print("\nSpatial separation at t=1ms:")
for dBdz in grad_B_vals:
    F = mu_B * g_e * dBdz
    dz = F / (2 * m_diam) * (1e-3)**2
    print(f"  ∂B/∂z = {dBdz:.0e} T/m → Δz = {dz*1e9:.3f} nm")
```

---

### 2.5 Gravity-Induced Phase vs. Diamond Separation

```python
import numpy as np
import matplotlib.pyplot as plt

G    = 6.674e-11   # m³/(kg·s²)
hbar = 1.055e-34
m    = 1e-17       # kg
dz   = 250e-6      # m, spatial superposition size
T    = 1.0         # s, total evolution time

r_vals = np.logspace(-5, -2, 300)  # 10µm to 1cm diamond separation

phi_grav = G * m**2 * dz / (hbar * r_vals**2) * T

fig, ax = plt.subplots(figsize=(10, 6), facecolor='#0a0a0a')
ax.set_facecolor('#0a0a0a')
ax.loglog(r_vals * 1e6, phi_grav, color='#FFD700', lw=2.5)
ax.axhline(1.0, color='#FF8C00', lw=1.5, ls='--', label='Δφ = 1 rad (max entanglement)')
ax.axhline(0.01, color='#FFA500', lw=1, ls=':', label='Δφ = 0.01 rad (detectable)')

# Mark optimal separation
r_opt = r_vals[np.argmin(np.abs(phi_grav - 1.0))]
ax.axvline(r_opt * 1e6, color='white', lw=1, alpha=0.5,
           label=f'r_opt ≈ {r_opt*1e6:.1f} µm')

ax.set_xlabel('Diamond separation r (µm)', color='white')
ax.set_ylabel('Gravitational phase Δφ (rad)', color='white')
ax.set_title('Gravity-Induced Entanglement Phase\n'
             r'$\Delta\phi = Gm^2\Delta z\, T / (\hbar r^2)$', color='#FFD700')
ax.tick_params(colors='white')
ax.legend(fontsize=9)
ax.grid(True, alpha=0.2)
plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/gravity_phase.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()

print(f"\nFor r=200µm, Δz=250µm, T=1s:")
r = 200e-6
phi = G * m**2 * dz / (hbar * r**2) * T
print(f"  Δφ_grav = {phi:.4e} rad")
print(f"  Need N > {int(1/phi**2)} trials to resolve")
```

---

### 2.6 Full Density Matrix Evolution with Decoherence (QuTiP)

```python
import numpy as np
import matplotlib.pyplot as plt
import qutip as qt

Sz = qt.jmat(1, 'z')
Sx = qt.jmat(1, 'x')
Sy = qt.jmat(1, 'y')

D       = 2.87e9 * 2 * np.pi
gamma_e = 28.0e9 * 2 * np.pi
B       = 1e-3

H = D * Sz**2 + gamma_e * B * Sz

T1   = 6e-3   # ms longitudinal
T2   = 2e-3   # ms transverse
T2s  = 2e-6   # µs inhomogeneous

c_ops = [
    np.sqrt(1/(2*T1)) * qt.destroy(3),    # relaxation
    np.sqrt(1/T2s) * Sz,                  # dephasing
]

# Initial state: equal superposition of |0> and |+1>
psi0 = (qt.basis(3, 1) + qt.basis(3, 2)).unit()
rho0 = qt.ket2dm(psi0)

# Evolve
t_list = np.linspace(0, 10e-6, 500)
opts   = qt.Options(nsteps=5000)
result = qt.mesolve(H, rho0, t_list, c_ops,
                    [Sz, Sx, Sy, qt.ket2dm(qt.basis(3,1))],
                    options=opts)

fig, axes = plt.subplots(2, 2, figsize=(14, 10), facecolor='#0a0a0a')
fig.suptitle('NV Centre Density Matrix Evolution', color='#FFD700', fontsize=14)

labels = [r'$\langle S_z \rangle$', r'$\langle S_x \rangle$',
          r'$\langle S_y \rangle$', r'$P_{|0\rangle}$']
colors = ['#FFD700', '#FFA500', '#FF8C00', '#FF4500']

for ax, exp_val, label, color in zip(axes.flat, result.expect, labels, colors):
    ax.set_facecolor('#0a0a0a')
    ax.plot(t_list * 1e6, exp_val, color=color, lw=2)
    ax.set_xlabel('Time (µs)', color='white')
    ax.set_ylabel(label, color='white')
    ax.set_title(label, color='#FFD700')
    ax.tick_params(colors='white')
    ax.grid(True, alpha=0.2)

plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/density_matrix_evolution.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()
```

---

<a name="phase1"></a>
## 3. Phase 1 — Hardware: NV Spin Superposition

### 3.1 Equipment Required

| Item | Specification | Source | Cost |
|---|---|---|---|
| Diamond with NV centres | Bulk, type Ib, NV ensemble OR single NV nanocrystal ~100nm | Adámas Nanotechnologies, Element Six | $50–$500 |
| 532 nm DPSS laser | ≥10 mW, CW, TEM00 | Thorlabs, Coherent | $500–$2000 |
| Objective lens | 100×, NA=1.3, oil immersion | Olympus, Nikon | $500–$2000 |
| Dichroic mirror | 550 nm longpass | Thorlabs | $200 |
| Bandpass filter | 650–750 nm | Thorlabs | $150 |
| Single-photon detector | SPAD or PMT | Excelitas SPCM, Hamamatsu | $2000–$5000 |
| Microwave source | 2–4 GHz, IQ modulation | Mini-Circuits, Windfreak | $300–$1000 |
| Microwave amplifier | +30 dBm, 2–4 GHz | Mini-Circuits ZHL-16W | $200 |
| Microwave antenna | Omega loop wire, 25µm diameter | DIY | $5 |
| Pulse sequencer | Swabian Pulse Streamer 8/2 | Swabian Instruments | $3000 |
| Lock-in amplifier | Stanford SR830 or similar | Stanford Research | $3000 (or DIY) |
| Optical table | Vibration-isolated | Newport, TMC | $3000–$10000 |
| xyz piezo stage | 50µm range, 1nm resolution | Physik Instrumente | $2000 |
| PC with DAQ | NI USB-6363 or similar | National Instruments | $500 |

**Minimum viable lab: ~$15k–$20k**  
**University-grade: ~$50k**

---

### 3.2 Optical Layout

```
[532nm laser] → [AOM] → [Beam expander] → [Dichroic mirror]
                                                    ↓
                                          [100x objective]
                                                    ↓
                                          [Diamond sample]
                                                    ↑
                              [Fluorescence: 637-800nm]
                                                    ↓
                              [Dichroic mirror reflects 532, passes 637+]
                                                    ↓
                              [650-750nm bandpass filter]
                                                    ↓
                              [Single-photon detector (SPAD)]
                                                    ↓
                              [Pulse counter / Time tagger]

[MW source] → [IQ mixer] → [Amplifier] → [Omega wire near diamond]
[Pulse sequencer] → controls AOM, IQ mixer, data acquisition
```

The **AOM (acousto-optic modulator)** gates the laser: on for initialisation and readout, off during microwave pulse sequences.

---

### 3.3 Step-by-Step Bring-Up

#### Stage 1 — Laser and optics alignment

1. Mount 532 nm laser, beam-expand to ~5 mm diameter.
2. Align through dichroic mirror and objective to focus on diamond surface. Use a white light source first to focus.
3. Collect fluorescence through the dichroic and bandpass filter onto SPAD.
4. **Check:** count rate should be >100 kcps (kilo-counts per second) on ensemble NV sample.

**Fault finding:**
- Low count rate (<10 kcps) → check focus; increase laser power; clean objective and coverslip with IPA
- Saturated SPAD → reduce laser power with ND filter; add ND2 before detector
- No counts at all → check SPAD power; verify 532 nm is blocked by bandpass filter (it should be); check dichroic orientation

#### Stage 2 — ODMR (find the spin resonance)

1. Apply CW microwave at ~2.87 GHz through the omega wire.
2. Sweep MW frequency from 2.5 to 3.2 GHz in 1 MHz steps.
3. Monitor fluorescence vs. MW frequency.
4. **Expected:** two dips in fluorescence at $f_\pm = 2.87 \pm \gamma_e B$ GHz.

```python
import serial
import numpy as np

# Assuming MW source controlled via VISA and counts via serial DAQ
freqs = np.arange(2.5e9, 3.2e9, 1e6)
counts = []

for f in freqs:
    # set_mw_frequency(f)        # your MW source command
    # counts.append(read_counts(integration_time=10e-3))
    pass  # replace with actual instrument calls

# Plot
import matplotlib.pyplot as plt
plt.plot(freqs/1e9, counts, 'gold')
plt.xlabel('MW Frequency (GHz)')
plt.ylabel('Fluorescence counts')
plt.title('ODMR Spectrum')
plt.show()
```

**Fault finding:**
- No dips visible → MW power too low; increase to +10 dBm at omega wire; check MW amplifier gain
- Dips too broad (>10 MHz) → MW power too high (power broadening); reduce by 6 dB
- Single dip only → static magnetic field B=0; place a small neodymium magnet ~5 cm away to lift degeneracy
- Dips asymmetric → NV axis not aligned with B field; rotate magnet

#### Stage 3 — Pulsed ODMR (calibrate π pulse)

1. Switch to pulsed mode: `init(3µs) → MW_pulse(t_p) → readout(300ns)`
2. Sweep pulse duration $t_p$ from 0 to 200 ns.
3. Plot fluorescence vs. $t_p$. Expect **Rabi oscillations**: $F(t_p) = F_0 - A\sin^2(\Omega t_p / 2)$.
4. $\pi$ pulse = first minimum (max spin flip). $\pi/2$ pulse = first half-minimum.

**Typical values:** $\pi$ pulse ~30–100 ns at MW power +20 dBm.

```python
def rabi_model(t, A, Omega, offset, phase):
    return offset - A * np.sin(Omega * t / 2 + phase)**2

# Fit to measured data
from scipy.optimize import curve_fit
popt, _ = curve_fit(rabi_model, t_pulse, F_measured,
                    p0=[0.3, 2*np.pi/100e-9, 0.85, 0])
t_pi  = np.pi / popt[1]
t_pi2 = t_pi / 2
print(f"π pulse: {t_pi*1e9:.1f} ns")
print(f"π/2 pulse: {t_pi2*1e9:.1f} ns")
```

**Fault finding:**
- No Rabi oscillations → MW frequency not on resonance; redo ODMR first
- Oscillations decay within 5 pulses → T2* very short; diamond quality poor; try a different sample
- Contrast too low (<5%) → too many NV centres in confocal volume; move to single-NV sample

#### Stage 4 — Ramsey Sequence

Pulse sequence:
```
[532nm 3µs] → [π/2 MW] → [wait τ] → [π/2 MW] → [532nm 300ns readout]
```

1. Programme this sequence on the pulse sequencer.
2. Sweep $\tau$ from 0 to 10 µs in 20 ns steps.
3. Average 1000 shots per point.
4. Fit the result to extract $T_2^*$ and the local magnetic field.

**Pass criterion:** Ramsey fringes visible; $T_2^* > 1\,\mu\text{s}$; fit $R^2 > 0.95$.

**Fault finding:**
- Fringes decay faster than 500 ns → detuning too large; tune MW closer to resonance OR the diamond has too much strain; try different NV
- No fringe pattern (flat signal) → check π/2 pulse calibration; rerun Rabi
- Fringe contrast <3% → single-photon detector dark counts too high; cool SPAD or add second bandpass filter

#### Stage 5 — Spin Echo

Replace the Ramsey sequence with:
```
[init] → [π/2] → [τ/2] → [π] → [τ/2] → [π/2] → [readout]
```

Sweep total time $\tau$ from 0 to 2 ms. Observe coherence extending from $T_2^*$ to $T_2$.

**Pass criterion:** $T_2 > 100\,\mu\text{s}$ for a good diamond; $T_2 > 1\,\text{ms}$ for isotopically purified $^{12}\text{C}$ diamond.

---

<a name="phase2"></a>
## 4. Phase 2 — Spin-Spatial Superposition

### 4.1 Magnetic Gradient Source

Two approaches:

**Option A — Sharp magnetic tip**
- Magnetised AFM cantilever tip (~50 nm radius) placed 100 nm above diamond
- Gradient $\partial B/\partial z \sim B_{tip}/r^2 \sim 1\text{ T}/(100\text{ nm})^2 = 10^8\,\text{T/m}$

**Option B — Current-carrying wire**
- 25 µm diameter copper wire carrying $I = 1\,\text{A}$
- At distance $d = 50\,\mu\text{m}$: $B = \mu_0 I/(2\pi d) = 4\,\text{mT}$
- Gradient: $\partial B/\partial z = \mu_0 I/(2\pi d^2) = 80\,\text{T/m}$
- To reach $10^6\,\text{T/m}$: need $d = 0.5\,\mu\text{m}$ — requires nanofabrication

**Force on diamond** for Option B at 50 µm:
$$F = \mu_B g_e \cdot 80\,\text{T/m} \approx 1.5 \times 10^{-21}\,\text{N}$$

For $m = 10^{-17}\,\text{kg}$, acceleration $a = F/m = 1.5 \times 10^{-4}\,\text{m/s}^2$.  
Separation in $T = 1\,\text{ms}$: $\Delta z = \frac{1}{2}aT^2 = 7.5 \times 10^{-11}\,\text{m} = 0.075\,\text{nm}$

This is very small but measurable as a phase shift:
$$\Delta\phi = \frac{m \Delta z^2}{2\hbar T} \approx 10^{-12}\,\text{rad}$$

This is below current measurement sensitivity. **Phase 2 requires $\partial B/\partial z \geq 10^6\,\text{T/m}$** — achievable only with near-field magnetic tips or superconducting flux qubits.

### 4.2 What to Build for Phase 2

```
Levitated nanodiamond + optical trap + magnetic gradient:

[Trapping laser 1064nm] → [Objective] → [Diamond nanocrystal in vacuum]
                                                 ↑
                          [Magnetic gradient from coil pair or tip]
                                                 ↑
                          [NV readout laser 532nm + detection as Phase 1]
```

**Key addition:** replace the fixed diamond on a coverslip with an **optically levitated** nanodiamond in vacuum. This decouples the diamond from phonon bath of the substrate and allows true centre-of-mass motion.

**Reference system:** Delord et al. (2018) — NV spin readout in a levitated nanodiamond. Conangla et al. (2020) — spin-mechanical coupling in levitated trap.

---

<a name="phase3"></a>
## 5. Phase 3 — Environmental Noise Reduction

### 5.1 Noise Budget

| Noise source | Magnitude | Mitigation |
|---|---|---|
| Seismic / vibrational | $10^{-9}\,\text{m/}\sqrt{\text{Hz}}$ at 1 Hz | Active isolation table; operate >10 Hz |
| Magnetic field drift | $1\,\text{nT/s}$ | µ-metal shield + active flux feedback |
| Thermal phonons | $k_BT/\hbar\omega_m$ occupancy | Dilution refrigerator to 10 mK |
| Gas molecule collisions | Decoherence $\propto P$ (pressure) | $P < 10^{-9}\,\text{Pa}$ vacuum |
| Laser intensity noise | $\Delta I/I \sim 10^{-4}$ | AOM intensity stabilisation |
| MW phase noise | $-100\,\text{dBc/Hz}$ at 1 kHz | Low phase-noise oscillator (Wenzel, Vectron) |
| Charge noise | $1/f$ electric field fluctuations | Charge-stable NV (negative charge state) |

### 5.2 Vacuum System

Target: $P < 10^{-9}\,\text{Pa}$ for levitation experiments.

Required pumping stages:
1. Roughing pump (rotary/scroll): $10^5 \to 10^1\,\text{Pa}$
2. Turbomolecular pump: $10^1 \to 10^{-7}\,\text{Pa}$
3. Ion pump or getter pump: $10^{-7} \to 10^{-11}\,\text{Pa}$

**Verify with:** residual gas analyser (RGA) — check for water, nitrogen, hydrocarbon peaks.

### 5.3 Magnetic Shielding

A 3-layer µ-metal shield (cylindrical, each layer 1 mm thick, 5 cm separation) provides:

$$B_{inside} = B_{outside} / \left(\frac{\mu_r t}{R}\right)^n \approx B_{outside} / 10^6$$

where $\mu_r \approx 100{,}000$ for annealed µ-metal, $t$ thickness, $R$ radius, $n$ number of layers.

**Verify with:** fluxgate magnetometer inside shield. Target: $B < 1\,\text{nT}$ at diamond location.

---

<a name="phase4"></a>
## 6. Phase 4 — Gravity Entanglement Test

### 6.1 Protocol

1. Prepare two diamonds $A$ and $B$, each with NV centre, separated by $r \approx 200\,\mu\text{m}$.
2. Initialise both spins: $|0\rangle_A \otimes |0\rangle_B$.
3. Apply $\pi/2$ to both → $(|0\rangle + |1\rangle)_A \otimes (|0\rangle + |1\rangle)_B / 2$.
4. Apply gradient: each diamond enters spatial superposition $\Delta z$.
5. Allow gravitational interaction for time $T$. The joint state evolves:

$$|\Psi(T)\rangle = \frac{1}{2}\left(e^{i\phi_{00}}|00\rangle_{AB} + e^{i\phi_{01}}|01\rangle_{AB} + e^{i\phi_{10}}|10\rangle_{AB} + e^{i\phi_{11}}|11\rangle_{AB}\right)$$

where $\phi_{ij}$ depends on the gravitational potential between configurations $i,j$.

6. Switch off gradient to recombine spatial superpositions.
7. Apply final $\pi/2$ to both and measure spin state of each.
8. Repeat $N \sim 10^6$ times to build correlation statistics.
9. Compute CHSH parameter $S$. If $|S| > 2$: entanglement confirmed.

### 6.2 What Entanglement Implies

If the two diamonds become entangled **and** the only interaction between them is gravitational **and** all EM interactions are shielded, then:

> **Gravity must have mediated quantum correlations, which a classical field cannot do.**

This would be the first experimental evidence that gravity is a quantum field — the most significant result in fundamental physics in decades.

### 6.3 CHSH Inequality Calculation

```python
import numpy as np

def CHSH_quantum_gravity(phi_grav, N_trials=1000000):
    """
    Simulate CHSH measurement for gravity-entangled diamonds.
    phi_grav: gravitational phase (rad)
    """
    # Entangled state after gravity: approximately |Phi+> for small phi
    # |Psi> = cos(phi/2)|00> + i*sin(phi/2)|11>
    
    theta_A  = [0, np.pi/4]           # Alice measurement angles
    theta_B  = [np.pi/8, 3*np.pi/8]  # Bob measurement angles
    
    # Quantum correlation: <A_i B_j> = cos(2*(theta_Ai - theta_Bj)) * sin(phi_grav)
    E = np.zeros((2, 2))
    for i, ta in enumerate(theta_A):
        for j, tb in enumerate(theta_B):
            E[i,j] = np.cos(2*(ta - tb)) * np.sin(phi_grav)
    
    S = E[0,0] - E[0,1] + E[1,0] + E[1,1]
    return S, E

phi_vals = np.linspace(0, np.pi/2, 100)
S_vals = [CHSH_quantum_gravity(phi)[0] for phi in phi_vals]

import matplotlib.pyplot as plt
fig, ax = plt.subplots(figsize=(10, 6), facecolor='#0a0a0a')
ax.set_facecolor('#0a0a0a')
ax.plot(phi_vals, S_vals, color='#FFD700', lw=2.5)
ax.axhline(2.0, color='#FF4500', lw=2, ls='--', label='Classical limit S=2')
ax.axhline(2*np.sqrt(2), color='#FFA500', lw=1.5, ls=':',
           label=r'Tsirelson bound $2\sqrt{2}$')
ax.fill_between(phi_vals, 2.0, S_vals,
                where=np.array(S_vals)>2.0, alpha=0.3, color='gold',
                label='Quantum violation region')
ax.set_xlabel('Gravitational phase Δφ (rad)', color='white')
ax.set_ylabel('CHSH parameter S', color='white')
ax.set_title('CHSH Bell Inequality — Gravity Entanglement Test', color='#FFD700')
ax.tick_params(colors='white')
ax.legend(fontsize=9)
ax.grid(True, alpha=0.2)
plt.tight_layout()
plt.savefig('/home/steve/Quantum gravity/CHSH_gravity.png',
            dpi=150, facecolor='#0a0a0a')
plt.show()
```

---

<a name="faults"></a>
## 7. Master Fault-Finding Reference

| Symptom | Most likely cause | Diagnostic | Fix |
|---|---|---|---|
| No fluorescence from diamond | Laser not focused on NV | Scan confocal XYZ | Refocus; clean objective |
| ODMR dips absent | MW not reaching diamond | Measure MW power at antenna | Check amplifier, cable connections |
| ODMR single dip | B=0, degenerate $|\pm1\rangle$ | Place neodymium magnet nearby | Add static field to lift degeneracy |
| Rabi oscillations not visible | MW frequency off-resonance | Rerun CW ODMR first | Set MW to ODMR dip frequency |
| $T_2^*$ < 100 ns | Poor diamond quality / high N concentration | Check diamond spec sheet | Use lower-N diamond; try different NV |
| Ramsey fringe contrast < 5% | Too many NVs in focal volume | Check if single NV ($g^2(0) < 0.5$) | Move to nanocrystal sample |
| Phase 0 simulation no oscillation | Zero detuning in simulation | Check $\omega_{01}$ vs $\omega_{mw}$ | Add detuning: `omega_mw *= 1.001` |
| QuTiP mesolve very slow | Too many time steps | Reduce `t_list` resolution | Use `max_step` parameter in Options |
| Spatial separation too small | Gradient too weak | Calculate $F = \mu_B g \partial B/\partial z$ | Need near-field magnetic tip |
| Gravity phase < noise floor | $r$ too large or $T$ too short | Use gravity phase formula | Reduce separation; increase $T$ (needs longer $T_2$) |
| CHSH S < 2 always | $\Delta\phi_{grav}$ too small | Check $\phi$ formula with actual params | Need larger mass or longer coherence |

---

<a name="safety"></a>
## 8. Safety

| Hazard | Risk | Control |
|---|---|---|
| 532 nm laser | Permanent retinal damage | OD5+ goggles for 532 nm; interlocked enclosure; never exceed 5 mW without enclosure |
| 1064 nm (levitation laser) | Invisible; worse than visible — no blink reflex | OD6+ goggles for 1064 nm; treat as Class 4 always |
| Cryogenics (LHe/LN2) | Asphyxiation, cryogenic burns | O2 monitor in lab; cryogenic gloves; face shield; never seal LHe dewar |
| High vacuum | Implosion of glass viewports | Use rated borosilicate or sapphire windows; check torque on flanges; never use cracked windows |
| Microwave RF (>+30 dBm) | RF burns to skin; eye damage | Never terminate MW into open air at >+20 dBm; use 50Ω loads; RF survey meter |
| Strong permanent magnets | Crush injury; pacemaker hazard | Post warning sign; keep >1m exclusion for pacemaker users; never allow two magnets to snap together near hands |
| Diamond nanoparticles | Inhalation hazard (ultrafine particles) | Work in fume hood when handling dry nanocrystals; use isopropanol suspension |

---

<a name="refs"></a>
## 9. References

1. **Marshman, R.J. et al.** (2020). *Mesoscopic interference for metric and curvature & gravitational wave detection.* New J. Phys. 22, 083012.
2. **Bose, S. et al.** (2017). *Spin Entanglement Witness for Quantum Gravity.* Phys. Rev. Lett. 119, 240401.
3. **Marletto, C. & Vedral, V.** (2017). *Gravitationally Induced Entanglement between Two Massive Particles is Sufficient Evidence of Quantum Effects in Gravity.* Phys. Rev. Lett. 119, 240402.
4. **Doherty, M.W. et al.** (2013). *The nitrogen-vacancy colour centre in diamond.* Phys. Rep. 528, 1–45.
5. **Toroš, M. et al.** (2021). *Relative acceleration noise mitigation for nanocrystal matter-wave interferometry.* Phys. Rev. Research 3, 023178.
6. **Delord, T. et al.** (2018). *Ramsey Interferences and Spin Echoes from Electron Spins Inside a Levitating Macroscopic Particle.* Phys. Rev. Lett. 121, 053602.
7. **Conangla, G.P. et al.** (2020). *Optimal Feedback Cooling of a Charged Levitated Nanoparticle with Adaptive Control.* Phys. Rev. Lett. 124, 203602.
8. **Weinfurtner, S. et al.** (2011). *Measurement of stimulated Hawking emission in an analogue system.* Phys. Rev. Lett. 106, 021302.

---

## Recommended Execution Order

```
Week 1:   Phase 0 — run all simulations; understand the physics
Week 2:   Obtain diamond sample; build confocal if available
Week 3:   Phase 1 Stage 1–2 — ODMR (laser + MW)
Week 4:   Phase 1 Stage 3–4 — Rabi + Ramsey
Week 5:   Phase 1 Stage 5 — Spin echo; measure T2
Month 2+: Phase 2 — magnetic gradient; optical levitation
Year 1+:  Phase 3 — vacuum, cryogenics, shielding
Year 3+:  Phase 4 — two-diamond entanglement
```

---

*Generated: 2026-07-04*
