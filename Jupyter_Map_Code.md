# Jupyter Notebook uchun Xarita Kodi

Quyidagi kodlarni **ketma-ket** ishga tushiring.

## ⚠️ Muhim Eslatma

Agar `HTTPError 404` xatoligi chiqsa, quyidagilardan birini qiling:

1. **Cartopy o'rnatish:**
```bash
pip install cartopy
# yoki
conda install -c conda-forge cartopy
```

2. **Yoki oddiy versiyadan foydalaning** (Cartopy kerak emas)

---

## Cell 1: Kutubxonalar va Sozlamalar

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches
import matplotlib.patheffects as pe
from matplotlib.lines import Line2D
import matplotlib.gridspec as gridspec
import warnings
warnings.filterwarnings('ignore')

# Cartopy import (agar o'rnatilgan bo'lsa)
try:
    import cartopy.crs as ccrs
    import cartopy.feature as cfeature
    from cartopy.mpl.gridliner import LONGITUDE_FORMATTER, LATITUDE_FORMATTER
    CARTOPY = True
    print("✅ Cartopy topildi")
except ImportError:
    CARTOPY = False
    print("⚠️  Cartopy topilmadi - oddiy versiya ishlatiladi")

# Koordinatalar
sites = {
    'Tonnelnaya Cave': {
        'lon': 67.23, 'lat': 38.40, 'elev': 3226,
        'marker': '*', 'size': 280, 'color': '#D73027', 'zorder': 6
    },
    'Boysun MS': {
        'lon': 67.21, 'lat': 38.19, 'elev': 1000,
        'marker': '^', 'size': 120, 'color': '#4393C3', 'zorder': 5
    },
}

cities = {
    'Tashkent': (69.24, 41.30), 'Samarkand': (66.92, 39.65),
    'Termez': (67.28, 37.22), 'Dushanbe': (68.77, 38.56),
    'Bishkek': (74.59, 42.87), 'Kabul': (69.18, 34.53),
}

countries = {
    'UZBEKISTAN': (64.5, 41.0), 'TAJIKISTAN': (71.0, 39.0),
    'KYRGYZSTAN': (74.5, 41.5), 'AFGHANISTAN': (66.0, 34.8),
    'TURKMENISTAN': (59.5, 39.5), 'KAZAKHSTAN': (63.0, 44.5),
}
```

---

## Cell 2: Xarita Yaratish (ODDIY VERSIYA - Cartopy kerak emas!)

```python
# Figure yaratish
fig = plt.figure(figsize=(18, 12), facecolor='white')
gs = gridspec.GridSpec(2, 2, width_ratios=[2.2, 1], height_ratios=[1.8, 1],
                       wspace=0.04, hspace=0.08, left=0.03, right=0.97,
                       top=0.93, bottom=0.06)

# ========== PANEL A: Asosiy xarita ==========
ax_main = fig.add_subplot(gs[:, 0])
ax_main.set_xlim(58, 78)
ax_main.set_ylim(34, 46)
ax_main.set_facecolor('#F5F0E8')
ax_main.set_aspect('equal')

# Mamlakat nomlari
for name, (lon, lat) in countries.items():
    ax_main.text(lon, lat, name, fontsize=8, color='#555555',
                 ha='center', style='italic', fontweight='bold',
                 path_effects=[pe.withStroke(linewidth=2, foreground='white')])

# Shaharlar
for city, (lon, lat) in cities.items():
    ax_main.plot(lon, lat, 'o', color='#555555', ms=4, zorder=4)
    ax_main.text(lon + 0.3, lat + 0.2, city, fontsize=8, color='#333333',
                 path_effects=[pe.withStroke(linewidth=2, foreground='white')])

# Study sites
for name, info in sites.items():
    ax_main.plot(info['lon'], info['lat'], marker=info['marker'],
                 color=info['color'], ms=np.sqrt(info['size']),
                 zorder=info['zorder'], markeredgecolor='white', markeredgewidth=1.5)
    offset_y = 0.3 if 'Tonnelnaya' in name else -0.5
    ax_main.text(info['lon'] + 0.5, info['lat'] + offset_y, name,
                 fontsize=9, color=info['color'], fontweight='bold',
                 path_effects=[pe.withStroke(linewidth=2.5, foreground='white')])

# Masofa chizig'i
ax_main.plot([67.23, 67.21], [38.40, 38.19], color='#888888',
             lw=1.0, ls='--', zorder=3)
ax_main.text(67.6, 38.30, '~25 km\n~2226 m Δelev', fontsize=8,
             color='#666666', ha='left',
             bbox=dict(boxstyle='round,pad=0.3', facecolor='white',
                      edgecolor='#CCCCCC', alpha=0.8))

ax_main.grid(True, alpha=0.3, linestyle='--', linewidth=0.4)
ax_main.set_xlabel('Longitude (°E)', fontsize=10)
ax_main.set_ylabel('Latitude (°N)', fontsize=10)
ax_main.set_title('Study area — Tonnelnaya Cave, southern Uzbekistan',
                  fontsize=12, fontweight='bold', pad=10, loc='left')

# Legend
legend_elements = [
    Line2D([0], [0], marker='*', color='w', markerfacecolor='#D73027',
           markersize=15, markeredgecolor='white',
           label='Tonnelnaya Cave (3226 m a.s.l.)'),
    Line2D([0], [0], marker='^', color='w', markerfacecolor='#4393C3',
           markersize=10, markeredgecolor='white',
           label='Boysun MS (1000 m a.s.l.)'),
    Line2D([0], [0], marker='o', color='w', markerfacecolor='#555555',
           markersize=6, label='City'),
]
ax_main.legend(handles=legend_elements, loc='lower left', fontsize=9,
               framealpha=0.95, edgecolor='#CCCCCC', title='Study sites')

# ========== PANEL B: Regional map ==========
ax_reg = fig.add_subplot(gs[0, 1])
ax_reg.set_xlim(40, 100)
ax_reg.set_ylim(20, 55)
ax_reg.set_facecolor('#F5F0E8')
ax_reg.set_aspect('equal')

# Study area box
ax_reg.add_patch(mpatches.Rectangle((58, 34), 20, 12, linewidth=2,
                                    edgecolor='#D73027', facecolor='none', zorder=5))
ax_reg.plot(67.23, 38.40, '*', color='#D73027', ms=10, zorder=6,
            markeredgecolor='white', markeredgewidth=1)

# Geographic labels
for name, (lon, lat) in [('Aral\nSea', (60.0, 45.0)),
                         ('Caspian\nSea', (51.0, 41.5)),
                         ('Pamir', (73.5, 37.5)),
                         ('Tian Shan', (78.0, 42.0)),
                         ('Hindu Kush', (68.0, 35.0))]:
    ax_reg.text(lon, lat, name, fontsize=7, color='#555555',
                ha='center', style='italic')

ax_reg.grid(True, alpha=0.2, linestyle='--', linewidth=0.3)
ax_reg.set_title('Regional context', fontsize=9, pad=4)
ax_reg.set_xlabel('Longitude (°E)', fontsize=8)
ax_reg.set_ylabel('Latitude (°N)', fontsize=8)

# ========== PANEL C: Elevation profile ==========
ax_elev = fig.add_subplot(gs[1, 1])

dist = np.array([0, 5, 10, 15, 20, 25])
elev = np.array([1000, 1400, 1900, 2500, 2900, 3226])
elev_sm = np.interp(np.linspace(0, 25, 100), dist, elev)
dist_sm = np.linspace(0, 25, 100)

ax_elev.fill_between(dist_sm, elev_sm, 800, color='#E8DCC8', alpha=0.8, zorder=1)
ax_elev.plot(dist_sm, elev_sm, color='#8B6914', lw=2.0, zorder=2)

ax_elev.plot(0, 1000, '^', color='#4393C3', ms=12, zorder=4,
             markeredgecolor='white', markeredgewidth=1.5)
ax_elev.text(0.5, 1050, 'Boysun MS\n(1000 m)', fontsize=8.5,
             color='#4393C3', fontweight='bold')

ax_elev.plot(25, 3226, '*', color='#D73027', ms=16, zorder=4,
             markeredgecolor='white', markeredgewidth=1.5)
ax_elev.text(21, 3300, 'Tonnelnaya\n(3226 m)', fontsize=8.5,
             color='#D73027', fontweight='bold', ha='right')

ax_elev.annotate('', xy=(25, 3226), xytext=(0, 1000),
                 arrowprops=dict(arrowstyle='<->', color='#666', lw=1.2))
ax_elev.text(13, 2100, 'Δh = 2226 m\nΓ = −3.57°C km⁻¹',
             fontsize=8, color='#555555', ha='center',
             bbox=dict(boxstyle='round,pad=0.3', facecolor='white',
                      edgecolor='#CCCCCC', alpha=0.9))

ax_elev.set_xlim(-1, 27)
ax_elev.set_ylim(700, 3600)
ax_elev.set_xlabel('Distance (km)', fontsize=9)
ax_elev.set_ylabel('Elevation (m a.s.l.)', fontsize=9)
ax_elev.set_title('Elevation profile', fontsize=9, pad=4)
ax_elev.grid(True, alpha=0.2, lw=0.5)
ax_elev.spines[['top','right']].set_visible(False)

# Caption
fig.text(0.03, 0.01,
         'Fig. 1.  Location of Tonnelnaya Cave (38.40°N, 67.23°E, 3226 m a.s.l.) '
         'and Boysun meteorological station (38.19°N, 67.21°E, 1000 m a.s.l.), '
         'southern Uzbekistan.',
         fontsize=8, color='#444444', style='italic', ha='left', va='bottom')

plt.tight_layout(rect=[0, 0.08, 1, 1])

# Saqlash
plt.savefig('fig1_study_site.png', dpi=300, bbox_inches='tight', facecolor='white')
plt.savefig('fig1_study_site.pdf', dpi=300, bbox_inches='tight', facecolor='white')

plt.show()

print("✅ Saqlandi:")
print("   - fig1_study_site.png")
print("   - fig1_study_site.pdf")
```

---

## 🎯 Tez O'zgartirishlar

### Rang o'zgartirish:
```python
# Tonnelnaya rangi
sites['Tonnelnaya Cave']['color'] = '#FF0000'  # Qizil

# Boysun rangi
sites['Boysun MS']['color'] = '#0000FF'  # Ko'k
```

### Koordinatalarni sozlash:
```python
# Zoom in/out
ax_main.set_xlim(62, 72)  # Kichikroq hudud
ax_main.set_ylim(36, 42)
```

### Marker o'lchami:
```python
sites['Tonnelnaya Cave']['size'] = 400  # Kattaroq
```

---

## ℹ️ Cartopy bilan ishlash (agar o'rnatilgan bo'lsa)

Agar Cartopy o'rnatilgan bo'lsa va professional xarita kerak bo'lsa:

```python
%run fig1_study_site_map.py
```

Yoki Python faylni ishga tushiring:
```bash
python fig1_study_site_map.py
```
