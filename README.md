# Pinigai

Greitas būdas analizuoti vidutines algas.

[Veikiantis puslapis](https://pinigai.ffff.lt)

## Pradėti darbą

Instaliuojame:

```bash
npm install
```

Paleidžiame:

```bash
npm run dev
```

Einame į [localhost:5000](http://localhost:5000).

## Duomenys

Duomenis reikia pasiimti ir apdoroti naudojant skriptus iš
[salary-data
repozitorijos](https://github.com/daliusd/salary-data).
Rekomenduoju naudoti monthly.js:

```
node monthly.js kelias/iki/pinigai/public/data
```

Šis skriptas parsiųs ir apdoros paskutinio mėnesio duomenis iš
Sodros.
