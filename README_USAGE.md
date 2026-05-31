# TON-2 Paleoclimate Figure - Foydalanish Yo'riqnomasi

## 🚀 Tez Boshlash

### 1. Python Skript ishlatish

```bash
python paleoclimate_figure_publication.py
```

### 2. Jupyter Notebook da ishlatish

Jupyter notebook da yangi cell ochib, quyidagi kodni copy qiling:

```python
# Bu kodni bitta cell da ishga tushiring!
%run paleoclimate_figure_publication.py
```

## ⚙️ Sozlamalar

`paleoclimate_figure_publication.py` faylini ochib, quyidagi qismlarni o'zgartiring:

### Ma'lumotlar fayli:
```python
DATA_FILE = 'sizning_fayl.csv'  # Fayl nomini o'zgartiring
FILE_TYPE = 'csv'  # yoki 'excel'
```

### Ustun nomlari:
```python
COLUMNS = {
    'age': 'age_calBP',              # Sizning ustun nomingiz
    'd18O_calcite': 'd18O_calcite',  
    'd18O_water': 'delta_d18O_water',
    'temperature': 'temp_anomaly',
    'precipitation': 'MAP_anomaly',
}
```

### Hiatus davrlari:
```python
HIATUS_PERIODS = [
    (36, 59),    # Birinchi uzilish (ka BP)
    (101, 144),  # Ikkinchi uzilish
]
# Yoki avtomatik topish uchun: HIATUS_PERIODS = []
```

### Muhim nuqtalar:
```python
ANNOTATIONS = {
    'LGM': {'age': 21, 'temp': -4.5, 'precip': -150},
    'MIS5e': {'age': 85, 'temp': 3.8, 'precip': 100},
    'Holocene': {'age': 8, 'temp': 2.0, 'precip': 180},
}
```

## 📊 Chiqish

Dastur 2 ta fayl yaratadi:
- `TON2_reconstruction_annotated.png` (300 DPI)
- `TON2_reconstruction_annotated.pdf` (300 DPI, journal uchun)

## 🎨 Ranglarni o'zgartirish

```python
COLORS = {
    'd18O_calcite': '#2E86AB',    # Ko'k
    'd18O_water': '#A23B72',      # Binafsha  
    'temperature': '#E63946',     # Qizil
    'precipitation': '#457B9D',   # Ochiq ko'k
}
```

## ❓ Savol-javoblar

**Q: Ma'lumotlar fayli yo'q bo'lsa nima bo'ladi?**
A: Dastur avtomatik test ma'lumotlar yaratadi va ishlaydi.

**Q: Jupyter da ishlatish mumkinmi?**
A: Ha! `%run paleoclimate_figure_publication.py` buyrug'i bilan.

**Q: Hiatus qanday topiladi?**
A: Agar `HIATUS_PERIODS = []` bo'lsa, 2 ka dan katta bo'shliqlar avtomatik topiladi.

**Q: Annotatsiya pozitsiyasini o'zgartirish mumkinmi?**
A: Ha! `ANNOTATIONS` dagi `age` va qiymatlarni o'zgartiring.
