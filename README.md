# Configurateur schéma gainable

Application React (Vite + Tailwind) prête à déployer sur Vercel.

## Structure exacte à respecter sur GitHub

```
(racine du dépôt)
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

**Important** : les fichiers `main.jsx`, `App.jsx` et `index.css` doivent être dans un dossier `src/`, pas à la racine — c'est l'erreur qui a bloqué le premier déploiement.

## Mettre à jour ton dépôt GitHub existant (le plus simple)

Sur la page de ton dépôt `GAINABLE` sur github.com :

1. Clique sur **"Add file" → "Upload files"**
2. Glisse-dépose TOUS les fichiers de ce zip (dézippé), en conservant la structure : les 3 fichiers du dossier `src/` doivent être déposés en entrant d'abord dans le dossier `src/` du dépôt (ou glisse le dossier `src` entier si ton navigateur le permet)
3. En bas, clique **"Commit changes"**

Vercel redéploiera automatiquement après ce commit.

## Déployer depuis zéro (si nouveau dépôt)

### Option 1 — Via GitHub
1. Crée un nouveau dépôt sur [github.com/new](https://github.com/new)
2. Upload tous les fichiers de ce zip en conservant la structure ci-dessus
3. Sur [vercel.com](https://vercel.com) → **"Add New Project"** → importe le dépôt
4. Vercel détecte Vite automatiquement → **"Deploy"**

### Option 2 — Via la CLI Vercel (terminal, sans GitHub)
```bash
npm install -g vercel
vercel login
vercel --prod
```

## Tester en local (optionnel)

```bash
npm install
npm run dev
```

## Points d'attention pour ton développeur

Voir `brief-developpeur-export-pdf.md` (fourni séparément) pour le détail sur l'impression et l'envoi par email, qui nécessitent d'être hors du sandbox Claude pour fonctionner pleinement.
