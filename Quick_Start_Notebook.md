# Jupyter Notebook uchun Tez Kod

Jupyter notebook da yangi cell yarating va quyidagi kodni **ketma-ket** ishga tushiring:

## Cell 1: Kutubxonalar va Sozlamalar

```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import warnings
warnings.filterwarnings('ignore')

# SOZLAMALAR - Bu qismni o'zgartiring!
DATA_FILE = 'TON2_data.csv'  # Sizning fayl nomingiz
age_col = 'age_calBP'
d18O_calc_col = 'd18O_calcite'
d18O_water_col = 'delta_d18O_water'
temp_col = 'temp_anomaly'
precip_col = 'MAP_anomaly'

# Hiatus davrlari (ka BP)
hiatus_periods = [
    (36, 59),
    (101, 144),
]

print("✅ Sozlamalar yuklandi")
```

## Cell 2: Ma'lumotlarni Yuklash

```python
# Ma'lumotlarni yuklash
try:
    df = pd.read_csv(DATA_FILE)
    print(f"✅ Ma'lumotlar yuklandi: {len(df)} qator")
except:
    print("⚠️  Ma'lumot topilmadi, test data yaratilmoqda...")
    # Test data
    age_bp = np.linspace(8000, 134000, 2007)
    age_ka = age_bp / 1000
    df = pd.DataFrame({
        age_col: age_bp,
        d18O_calc_col: -5 + np.sin(age_ka/10) * 2 + np.random.randn(len(age_ka)) * 0.3,
        d18O_water_col: -3 + np.sin(age_ka/10) * 1.5 + np.random.randn(len(age_ka)) * 0.2,
        temp_col: -2 + 4*np.sin(age_ka/15) + np.random.randn(len(age_ka)) * 0.5,
        precip_col: 50 + 150*np.sin(age_ka/12) + np.random.randn(len(age_ka)) * 20,
    })
    print("✅ Test data yaratildi")

age_ka = df[age_col].values / 1000
```

## Cell 3: Rasmni Yaratish (TO'LIQ KOD)

```python
# Style
plt.style.use('default')
plt.rcParams['font.family'] = 'Arial'
plt.rcParams['font.size'] = 10
plt.rcParams['figure.dpi'] = 300

# Figure yaratish
fig, axes = plt.subplots(3, 1, figsize=(10, 12), sharex=True)
fig.subplots_adjust(hspace=0.05)

# ========== PANEL A: δ¹⁸O ==========
ax1 = axes[0]
d18O_calc = df[d18O_calc_col].values
line1 = ax1.plot(age_ka, d18O_calc, color='#2E86AB', linewidth=1.5, 
                 label='δ¹⁸O calcite (‰ VPDB)', zorder=3)
ax1.fill_between(age_ka, d18O_calc - 0.1, d18O_calc + 0.1, 
                 color='#2E86AB', alpha=0.2, zorder=2)

ax1_twin = ax1.twinx()
d18O_water = df[d18O_water_col].values
line2 = ax1_twin.plot(age_ka, d18O_water, color='#A23B72', linewidth=1.5,
                      linestyle='--', label='Δδ¹⁸O water (‰ VSMOW)', zorder=3)
ax1_twin.fill_between(age_ka, d18O_water - 0.05, d18O_water + 0.05,
                      color='#A23B72', alpha=0.15, zorder=2)

lines = line1 + line2
labels = [l.get_label() for l in lines]
ax1.legend(lines, labels, loc='upper left', frameon=True, 
          fancybox=False, edgecolor='black', fontsize=9)

ax1.set_ylabel('δ¹⁸O calcite (‰ VPDB)', fontsize=11, fontweight='bold')
ax1_twin.set_ylabel('Δδ¹⁸O water (‰ VSMOW)', fontsize=11, fontweight='bold')
ax1.grid(True, alpha=0.3, linestyle=':', linewidth=0.5)
ax1.text(0.02, 0.95, 'a', transform=ax1.transAxes, fontsize=14, 
         fontweight='bold', va='top', bbox=dict(boxstyle='round,pad=0.3', 
         facecolor='white', edgecolor='none', alpha=0.8))

# ========== PANEL B: Temperature ==========
ax2 = axes[1]
temp = df[temp_col].values
ax2.plot(age_ka, temp, color='#E63946', linewidth=1.5, zorder=3)
ax2.fill_between(age_ka, temp - 0.8, temp + 0.8, 
                 color='#E63946', alpha=0.25, zorder=2)
ax2.axhline(y=0, color='gray', linestyle='--', linewidth=1, alpha=0.5, zorder=1)

# Annotatsiyalar
ax2.annotate('LGM\\n-4.5°C', xy=(21, -4.5), xytext=(36, -6.5),
            arrowprops=dict(arrowstyle='->', lw=1.5, color='black'),
            fontsize=9, fontweight='bold', ha='left',
            bbox=dict(boxstyle='round,pad=0.4', facecolor='white', 
                     edgecolor='black', linewidth=1))

ax2.annotate('MIS 5e\\n+3.8°C', xy=(85, 3.8), xytext=(65, 5.3),
            arrowprops=dict(arrowstyle='->', lw=1.5, color='black'),
            fontsize=9, fontweight='bold', ha='right',
            bbox=dict(boxstyle='round,pad=0.4', facecolor='white',
                     edgecolor='black', linewidth=1))

ax2.annotate('Holocene\\nOptimum', xy=(8, 2.0), xytext=(20, 3.5),
            arrowprops=dict(arrowstyle='->', lw=1.5, color='black'),
            fontsize=9, fontweight='bold', ha='left',
            bbox=dict(boxstyle='round,pad=0.4', facecolor='white',
                     edgecolor='black', linewidth=1))

ax2.set_ylabel('Temperature anomaly (°C)', fontsize=11, fontweight='bold')
ax2.grid(True, alpha=0.3, linestyle=':', linewidth=0.5)
ax2.set_ylim(-6, 6)
ax2.text(0.02, 0.95, 'b', transform=ax2.transAxes, fontsize=14,
         fontweight='bold', va='top', bbox=dict(boxstyle='round,pad=0.3',
         facecolor='white', edgecolor='none', alpha=0.8))

# ========== PANEL C: Precipitation ==========
ax3 = axes[2]
precip = df[precip_col].values
ax3.plot(age_ka, precip, color='#457B9D', linewidth=1.5, zorder=3)
ax3.fill_between(age_ka, precip - 30, precip + 30,
                 color='#457B9D', alpha=0.25, zorder=2)
ax3.axhline(y=0, color='gray', linestyle='--', linewidth=1, alpha=0.5, zorder=1)

# Annotatsiyalar
ax3.annotate('Holocene peak\\n+180 mm', xy=(8, 180), xytext=(23, 230),
            arrowprops=dict(arrowstyle='->', lw=1.5, color='black'),
            fontsize=9, fontweight='bold', ha='left',
            bbox=dict(boxstyle='round,pad=0.4', facecolor='white',
                     edgecolor='black', linewidth=1))

ax3.annotate('LGM\\n-150 mm', xy=(21, -150), xytext=(36, -200),
            arrowprops=dict(arrowstyle='->', lw=1.5, color='black'),
            fontsize=9, fontweight='bold', ha='left',
            bbox=dict(boxstyle='round,pad=0.4', facecolor='white',
                     edgecolor='black', linewidth=1))

ax3.set_xlabel('Age (ka BP) → older', fontsize=12, fontweight='bold')
ax3.set_ylabel('MAP anomaly (mm)', fontsize=11, fontweight='bold')
ax3.grid(True, alpha=0.3, linestyle=':', linewidth=0.5)
ax3.set_ylim(-300, 300)
ax3.text(0.02, 0.95, 'c', transform=ax3.transAxes, fontsize=14,
         fontweight='bold', va='top', bbox=dict(boxstyle='round,pad=0.3',
         facecolor='white', edgecolor='none', alpha=0.8))

# ========== HIATUS zonalari ==========
for ax_idx, ax in enumerate(axes):
    for hiatus_start, hiatus_end in hiatus_periods:
        ax.axvspan(hiatus_start, hiatus_end, color='gray', alpha=0.15, zorder=0)
        if ax_idx == 0:
            mid_point = (hiatus_start + hiatus_end) / 2
            ylim = ax.get_ylim()
            y_pos = ylim[0] + (ylim[1] - ylim[0]) * 0.85
            ax.text(mid_point, y_pos, 'HIATUS', ha='center', va='top', 
                   fontsize=8, style='italic', color='gray', 
                   fontweight='bold', rotation=90, alpha=0.7)

ax3.set_xlim(age_ka.max(), age_ka.min())

# ========== Caption ==========
caption = """Fig. X. TON-2 speleothem quantitative paleoclimate reconstruction. (a) δ¹⁸O calcite 
(blue solid line, VPDB scale) and Δδ¹⁸O water (purple dashed line, VSMOW scale) with analytical 
uncertainties (shaded areas). (b) Temperature anomaly reconstruction relative to present day with 
key climatic periods annotated. (c) Mean annual precipitation (MAP) anomaly. Gray vertical bars 
indicate hiatus periods. All ages are calibrated radiocarbon years before present (ka BP)."""

fig.text(0.12, 0.01, caption, wrap=True, fontsize=8, 
        verticalalignment='bottom', style='italic')

plt.tight_layout(rect=[0, 0.08, 1, 1])

# Saqlash
plt.savefig('TON2_reconstruction_annotated.png', dpi=300, bbox_inches='tight')
plt.savefig('TON2_reconstruction_annotated.pdf', dpi=300, bbox_inches='tight')

plt.show()

print("✅ Rasmlar saqlandi:")
print("   - TON2_reconstruction_annotated.png")
print("   - TON2_reconstruction_annotated.pdf")
```

## 🎯 Tez O'zgartirish

Agar faqat annotatsiya pozitsiyasini o'zgartirmoqchi bo'lsangiz, Cell 3 dagi
`xy=(21, -4.5)` va `xytext=(36, -6.5)` qiymatlarini o'zgartiring.

Rang o'zgartirish uchun:
- `color='#2E86AB'` → boshqa rang kodi (masalan `'red'` yoki `'#FF0000'`)
