# Guide pas à pas : tester le CI/CD du projet 4CITE

Objectif : prouver que les pipelines PR et Main fonctionnent, avec des preuves montrables à l'oral.

---

## Pré-requis

- Avoir Docker Desktop lancé
- Avoir Node.js 20+
- Avoir un compte GitHub
- Être dans la racine du projet

Vérifications rapides :

```bash
node -v
npm -v
docker --version
docker-compose --version
```

---

## Étape 0 - Publier le projet sur GitHub

Statut : [ ]

1. Vérifier que Git est initialisé :

```bash
git status
```

2. Si le dossier n'est pas encore un dépôt Git, initialiser :

```bash
git init
```

3. Créer un repository vide sur GitHub (sans README, sans .gitignore, sans licence).

4. Ajouter le remote vers ton repo (remplace l'URL) :

```bash
git remote add origin https://github.com/<ton-user>/<ton-repo>.git
```

5. Vérifier la branche locale. Tes workflows écoutent `master`.

```bash
git branch
```

6. Si tu es sur `main`, renommer en `master` (ou adapte les workflows) :

```bash
git branch -M master
```

7. Faire le premier commit puis push :

```bash
git add .
git commit -m "chore: initial project import"
git push -u origin master
```

8. Vérifier sur GitHub que le code et les fichiers workflows sont visibles.

Preuve à garder :
- Capture du repo GitHub avec l'arborescence
- Capture de la branche `master`

---

## Étape 1 - Vérifier les workflows présents

Statut : [ ]

1. Ouvrir le fichier du pipeline PR : .github/workflows/pr.yml
2. Ouvrir le fichier du pipeline Main : .github/workflows/main.yml
3. Vérifier les déclencheurs :
   - PR pipeline : pull_request vers master
   - Main pipeline : push vers master

Preuve à garder (capture) :
- Les 2 fichiers ouverts avec les sections on: pull_request et on: push

---

## Étape 2 - Préparer l'environnement local

Statut : [ ]

1. Installer les dépendances exactes CI :

```bash
npm ci
```

2. Lancer la base (si nécessaire) :

```bash
docker-compose up -d
```

3. Vérifier que PostgreSQL est prêt :

```bash
docker-compose ps
```

Preuve à garder :
- Sortie npm ci sans erreur
- État du conteneur postgres en running

---

## Étape 3 - Rejouer le pipeline PR en local

Statut : [ ]

Exécuter dans cet ordre :

```bash
npm run lint
npm run typecheck
npm test
npm run test:frontend
npx playwright install --with-deps chromium
npm run test:e2e
npm audit --omit=dev
```

Important :
- Si une commande échoue, corriger puis relancer la commande échouée
- Une fois tout vert, noter que le projet est prêt pour la PR

Preuve à garder :
- Sortie terminal des commandes clés (lint, tests backend, tests frontend, e2e)

---

## Étape 4 - Créer une branche de test CI

Statut : [ ]

1. Créer une branche :

```bash
git checkout -b chore/test-ci-pipeline
```

2. Faire un mini changement sans risque (exemple dans README)
3. Commit + push :

```bash
git add .
git commit -m "chore: test CI pipeline"
git push -u origin chore/test-ci-pipeline
```

Preuve à garder :
- La branche visible sur GitHub

---

## Étape 5 - Déclencher et valider le PR Pipeline

Statut : [ ]

1. Ouvrir une PR vers master
2. Aller dans l'onglet Actions
3. Vérifier le workflow PR Pipeline :
   - lint
   - typecheck
   - backend tests
   - frontend tests
   - e2e tests
   - security audit
4. Attendre que tous les checks passent au vert

Preuve à garder :
- Capture de la PR avec checks verts
- Capture du run Actions terminé en success

---

## Étape 6 - Tester le blocage (option forte pour oral)

Statut : [ ]

But : montrer qu'un pipeline protège vraiment la qualité.

1. Ajouter volontairement une erreur de lint
2. Push sur la même branche
3. Vérifier que la PR passe en échec
4. Corriger l'erreur
5. Re-push
6. Vérifier retour au vert

Preuve à garder :
- 1 capture PR en échec
- 1 capture PR redevenue verte

---

## Étape 7 - Merge et validation du Main Pipeline

Statut : [ ]

1. Merger la PR (quand tout est vert)
2. Aller dans Actions
3. Vérifier le workflow Main Pipeline :
   - test
   - build
   - deploy (echo)

Preuve à garder :
- Capture du pipeline Main en success

---

## Étape 8 - Vérifier les protections de branche

Statut : [ ]

Dans GitHub > Settings > Branches > Branch protection rules :

- Require a pull request before merging
- Require approvals (au moins 1)
- Require status checks to pass before merging
- Sélectionner les checks du PR pipeline

Preuve à garder :
- Capture des règles activées

---

## Étape 9 - Rejouer le build local de production

Statut : [ ]

```bash
node ace build
```

Preuve à garder :
- Build local réussi sans erreur

---

## Étape 10 - Préparer les preuves pour l'oral

Statut : [ ]

Checklist des éléments à montrer pendant la soutenance :

- [ ] PR pipeline vert
- [ ] Main pipeline vert
- [ ] Exemple d'échec puis correction
- [ ] Règles de protection de branche
- [ ] Build local OK

Pitch conseillé en 20 secondes :

"On a validé le CI/CD à trois niveaux : local, PR et main. La PR exécute lint, typecheck, tests backend/frontend/e2e et audit sécurité. Le merge vers master déclenche test + build + deploy simulé. On a aussi validé les protections de branche avec review obligatoire et checks requis." 

---

## Journal d'exécution (à remplir)

Date :

- Étape 0 :
- Étape 1 :
- Étape 2 :
- Étape 3 :
- Étape 4 :
- Étape 5 :
- Étape 6 :
- Étape 7 :
- Étape 8 :
- Étape 9 :
- Étape 10 :

Résultat final : [ ] CI/CD validé  [ ] À corriger
