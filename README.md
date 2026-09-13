# Configurateur schéma gainable

Application React (Vite + Tailwind) prête à déployer sur Vercel.

## Structure

```
gainable-configurator/
├── package.json
├── vite.config.js
├── tailwind.config.js
├── postcss.config.js
├── vercel.json
├── index.html
└── src/
    ├── main.jsx
    ├── App.jsx      ← le configurateur complet
    └── index.css
```

## Tester en local (optionnel, avant de déployer)

Nécessite [Node.js](https://nodejs.org/) installé (version 18 ou plus récente).

```bash
npm install
npm run dev
```

Puis ouvrir l'adresse affichée dans le terminal (en général `http://localhost:5173`).

## Déployer sur Vercel

### Option 1 — Via GitHub (recommandé)

1. Crée un nouveau dépôt sur [GitHub](https://github.com/new)
2. Dans ce dossier, exécute :
   ```bash
   git init
   git add .
   git commit -m "Configurateur gainable"
   git branch -M main
   git remote add origin https://github.com/TON-COMPTE/TON-REPO.git
   git push -u origin main
   ```
3. Va sur [vercel.com](https://vercel.com), clique **"Add New Project"**
4. Importe le dépôt GitHub que tu viens de créer
5. Vercel détecte automatiquement Vite (grâce à `vercel.json`) — laisse les réglages par défaut
6. Clique **"Deploy"**

Ton site sera en ligne en 1-2 minutes, avec une URL du type `https://ton-projet.vercel.app`.

### Option 2 — Via la CLI Vercel (sans GitHub)

```bash
npm install -g vercel
vercel login
vercel --prod
```

Suis les instructions à l'écran (choisir le dossier courant, accepter les réglages par défaut détectés).

## Points d'attention pour ton développeur

- Le bouton **"Télécharger le PDF"** dépend de deux librairies chargées depuis un CDN (`html2canvas-pro` et `jsPDF`) — voir le fichier `brief-developpeur-export-pdf.md` fourni séparément pour le détail des recommandations (notamment : passer ces librairies en dépendances npm plutôt qu'en CDN, pour plus de fiabilité en production).
- Une fois déployé sur Vercel (donc **hors de l'iframe sandboxé de Claude**), les fonctions natives du navigateur (impression, téléchargement, pop-up) devraient fonctionner normalement sans les blocages rencontrés pendant le prototypage.
