# Maxime pe clase – aplicație Vue 3

Aplicație web simplă dezvoltată cu **Vue 3 + Vite** care primește un șir de numere întregi între 100 și 999, calculează **maximele pe clase** (ex: 100, 200, 300...) și le transformă în caractere ASCII. Rezultatul este afișat într-o grilă 4x4 interactivă.

---

# Funcționalități

- Validare input numeric (doar numere între 100–999, separate prin virgulă)
- Calculul maximului pe fiecare clasă de sute
- Conversie `max % 26 + 65` → caracter ASCII (A–Z)
- Afișare caractere într-o grilă 4x4 cu shuffle
- Click pe celule → afișare/ascundere literă

---

# Tehnologii folosite

- Vue 3 (Composition API)
- Vite
- HTML + CSS (fără librării externe)

---

# Rulare locală

1. Clonează proiectul:
```bash
git clone https://github.com/tomaadriandaniel/proiect-maxime.git
cd proiect-maxime

2. Instalează dependențele:

npm install

3. Rulează aplicația:

npm run dev