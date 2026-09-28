# Configurateur Gainable — projet Vercel

Application React (Vite + Tailwind) prête à déployer, contenant le configurateur
de systèmes gainables (unités, plénums, grilles, gaines, électricité,
accessoires, remises, schéma SVG en direct, listing des produits).

## Structure du projet

```
gainable-configurator/
├── index.html
├── package.json
├── postcss.config.js
├── tailwind.config.js
├── vercel.json
├── vite.config.js
├── .gitignore
├── README.md
├── brief-developpeur-export-pdf.md   ← lire en priorité (export PDF / email)
└── src/
    ├── main.jsx
    ├── App.jsx        ← le composant configurateur (fichier principal)
    └── index.css
```

**Important** : tous les fichiers du dossier `src/` doivent bien rester
DANS ce dossier `src/` une fois uploadés sur GitHub. Si vous glissez-déposez
les fichiers un par un via l'interface web de GitHub ("Upload files"), vérifiez
que `main.jsx`, `App.jsx` et `index.css` atterrissent bien dans `src/main.jsx`,
`src/App.jsx`, `src/index.css` et pas à la racine du dépôt — sinon le build
Vercel échoue avec une erreur du type :
`Rollup failed to resolve import "/src/main.jsx" from "/vercel/path0/index.html"`

## Déploiement — option A (recommandée) : GitHub + Vercel

1. Créer un nouveau dépôt sur GitHub (ex. `gainable-configurator`).
2. Uploader **tout le contenu de ce dossier** en conservant l'arborescence
   (idéalement via `git push`, voir option B ci-dessous — plus fiable que le
   glisser-déposer web qui peut aplatir les dossiers).
3. Sur [vercel.com](https://vercel.com), cliquer "Add New Project", importer
   le dépôt GitHub.
4. Vercel détecte automatiquement Vite grâce à `vercel.json`. Laisser les
   réglages par défaut et cliquer "Deploy".
5. Le nom du projet Vercel doit être en **minuscules uniquement**
   (ex. `gainable-config`, pas `Gainable-Config`).

## Déploiement — option B : en ligne de commande (plus fiable)

```bash
cd gainable-configurator
npm install
npm approve-scripts --allow-scripts-pending   # autorise le postinstall d'esbuild (sécurité npm)
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<votre-compte>/gainable-configurator.git
git push -u origin main
```

Puis sur Vercel : "Add New Project" → importer ce dépôt → Deploy.

Ou directement sans GitHub, avec la CLI Vercel :
```bash
npm install -g vercel
vercel --prod
```

## Erreur fréquente : postinstall esbuild bloqué

Si le build Vercel échoue avec un message du type
`npm warn allow-scripts ... esbuild@x.x.x (postinstall: node install.js)`,
c'est le système d'approbation de scripts npm qui bloque l'installation
d'esbuild (dépendance de Vite). Corriger en local puis pousser le résultat :

```bash
npm install
npm approve-scripts --allow-scripts-pending
git add package-lock.json
git commit -m "Approve esbuild postinstall script"
git push
```

## Prochaine étape technique : export PDF / envoi par email

Le bouton d'impression actuel (`window.print()`) fonctionne nativement une
fois le site déployé hors du bac à sable de Claude.ai. Pour aller plus loin
(PDF téléchargeable, envoi par email automatique), voir le fichier
`brief-developpeur-export-pdf.md` inclus dans ce dossier : il détaille ce qui
a été testé, pourquoi certaines approches échouaient dans l'environnement
Claude, et les deux options recommandées (génération PDF côté client avec
html2canvas-pro + jsPDF, ou génération PDF côté serveur avec Puppeteer —
recommandé pour un rendu fiable et l'envoi par email).
