# Brief technique — Export PDF / Email du configurateur gainable

## Contexte

Un configurateur de systèmes gainables (climatisation/chauffage — unités intérieures/extérieures, réseau de gaines, grilles, plénums, accessoires, électricité) a été prototypé en React sous forme d'artifact Claude (fichier unique `configurateur-gainable.jsx`, ci-joint). Il fonctionne bien pour la configuration elle-même (sélecteurs, calcul de prix, total estimatif, schéma SVG en direct).

**Le point bloquant** : générer et exporter un PDF (schéma + listing de produits + total) et l'envoyer par email. Plusieurs approches ont été testées dans l'environnement Claude et ont toutes échoué — **pas à cause d'un bug du code**, mais parce que Claude affiche cet artifact dans un `<iframe>` fortement restreint (sandbox) qui bloque des API navigateur normalement disponibles. Une fois le composant intégré dans un vrai site (hors de ce sandbox), ces restrictions n'existeront plus.

## Ce qui a été tenté et pourquoi ça a échoué dans le sandbox Claude

| Approche | Résultat dans le sandbox Claude | Cause |
|---|---|---|
| `html2canvas` + `jsPDF` (chargés via CDN) | Erreur `oklab` puis échec de chargement | Bug connu de html2canvas 1.4.1 avec les couleurs CSS modernes (`oklab`/`oklch`) ; le CDN de secours (jsdelivr) est bloqué par le sandbox |
| `window.open()` pour ouvrir un nouvel onglet | Bloqué comme pop-up | L'ouverture différée (après un `await`) perd le "geste utilisateur" ; même corrigée, le sandbox de l'iframe bloque probablement les pop-ups sortants |
| `window.print()` | Ne déclenche rien | Le sandbox de l'iframe ne propage pas l'action d'impression système |
| `navigator.clipboard.write()` (copie d'image) | Résultat incertain / non collable | Permission `clipboard-write` probablement non accordée à l'iframe par la page parente |
| `showSaveFilePicker()` | Non supporté sur mobile de toute façon | API desktop uniquement (Chrome/Edge) |

**Conclusion : rien de tout cela n'est un problème du code React lui-même.** Sur un vrai site (pas dans un iframe sandboxé), `window.print()`, les téléchargements de fichiers et l'ouverture de nouveaux onglets fonctionnent normalement.

## Ce qu'il faut implémenter

### Option A — Génération PDF côté client (rapide, correcte pour la plupart des cas)

- Utiliser `html2canvas-pro` (fork corrigeant le bug `oklab`/`oklch`/`color-mix`) **ou** une version récente de `html2canvas` — **en dépendance npm**, pas en CDN, pour éviter tout souci de chargement.
- `jsPDF` pour assembler les pages (déjà utilisé dans le prototype, fonctionne bien).
- Déclencher le téléchargement via un simple lien `<a download>` — sur un vrai site (hors iframe sandboxé), ça fonctionne nativement sans blocage pop-up.
- Le code existant dans `configurateur-gainable.jsx` (fonctions `buildPdfBlob`, `handleGeneratePdf`, actuellement inutilisées mais toujours présentes dans le fichier) peut servir de base — il faut juste remplacer le chargement CDN par un `import` npm classique.

### Option B — Génération PDF côté serveur (recommandé, plus robuste)

Plus fiable pour un rendu pixel-perfect et pour l'envoi par email :

1. Le front-end envoie au back-end la configuration complète (JSON de l'état du configurateur : unité, pièces, plénums, accessoires, remises, etc.).
2. Le back-end (Node.js) reconstruit une page HTML du schéma + listing (ou réutilise un rendu headless du composant React) et génère le PDF avec **Puppeteer** ou **Playwright** (`page.pdf()`), sans les limitations d'un navigateur sandboxé.
3. Le PDF généré est soit retourné en téléchargement au client, soit joint directement à un email envoyé côté serveur (voir ci-dessous).

C'est l'option la plus robuste car elle évite complètement les limitations des navigateurs (bloqueurs de pop-up, permissions clipboard, blocage html2canvas, etc.), et donne un rendu identique quel que soit l'appareil du client final.

### Envoi par email

Implémenter un endpoint back-end (ex. `POST /api/devis/envoyer`) qui :
- Reçoit la configuration (ou directement le PDF généré selon l'option choisie ci-dessus)
- Génère/joint le PDF
- Envoie l'email via un service transactionnel (SendGrid, Mailgun, AWS SES, ou SMTP via Nodemailer)
- Adresse destinataire : paramétrable dans le formulaire (actuellement testé avec `suchaildeveloppement@gmail.com`)

Ne pas utiliser `mailto:` côté client pour joindre un fichier — **c'est techniquement impossible**, aucun navigateur ne le permet (restriction universelle, pas un bug).

## Fichier fourni

`configurateur-gainable.jsx` — composant React unique (~330 Ko), autonome, sans dépendances externes autres que `lucide-react`. Contient :
- Toute la logique de configuration (marques Daikin/Mitsubishi/Midea/Toshiba/Sinclair/LG/Atlantic, plénums, grilles, accessoires, remises)
- Le calcul du total (`computeGrandTotal`)
- Le schéma SVG généré dynamiquement
- Le listing détaillé avec photos
- Les fonctions PDF/impression désormais désactivées dans l'UI (boutons retirés) mais toujours présentes dans le code, à réactiver/adapter selon l'option choisie ci-dessus

## Critères d'acceptation

- [ ] Bouton "Télécharger le PDF" fonctionne sur mobile (iOS Safari + Chrome Android) et desktop, sans dépendre de pop-ups autorisés
- [ ] Le PDF contient le schéma (image nette, pas de coupure) + le listing complet avec prix + le total estimatif
- [ ] Bouton "Envoyer par email" fonctionne de bout en bout (saisie d'une adresse, envoi réel, PDF en pièce jointe)
- [ ] Aucune dépendance chargée depuis un CDN externe en production (tout en `npm install`, bundlé)
- [ ] Testé sur au moins : iPhone Safari, Android Chrome, desktop Chrome
