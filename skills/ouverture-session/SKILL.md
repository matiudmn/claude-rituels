---
name: ouverture-session
description: Rituel d'ouverture d'une session de code (une session = un chantier). Cadre le projet et l'état git, reprend le contexte, qualifie le travail (type, taille, sensibilité), fixe un objectif vérifiable, prescrit les 3 à 5 skills adaptés parmi ceux installés, affiche la feuille de route de la session, puis déclenche lui-même chaque skill au bon point de contrôle. Utiliser quand l'utilisateur démarre une session sur une application, une fonctionnalité ou un item de backlog, dit « on ouvre une session », « on démarre », « je prends tel item du backlog », « nouvelle session sur [projet] ». Symétrique de cloture-session. Ne pas utiliser quand l'utilisateur délègue tout le chantier (c'est bras-droit), ni pour une session de contenu ou d'administration.
argument-hint: [projet et item du backlog, en une phrase]
---

# Ouverture de session

Une session = un chantier. Ouvrir proprement : bon dossier, bonne branche, contexte
repris, objectif vérifiable, et les bons skills prévus AVANT de coder, pour que la qualité
ne dépende pas de la mémoire de l'utilisateur. Ici l'utilisateur pilote et tu co-pilotes ;
quand il veut déléguer le chantier entier, c'est `bras-droit`. Chantier du jour :
`$ARGUMENTS`.

## Règles permanentes

- **Aiguilleur, pas pipeline** : prescrire 3 à 5 skills, jamais la liste entière. Chaque
  skill lancé coûte des tokens ; un skill qui ne change pas le résultat ne se lance pas.
- **Proportionné** : un correctif de moins d'une heure reçoit la trame minimale (objectif,
  test, branche). La trame complète est pour une fonctionnalité ou un item de backlog.
- **Code uniquement** : session de contenu ou d'administration ? L'annoncer et sortir du
  skill. Le rangement d'un dépôt (docs, scripts, assets, config) compte comme du code.
- **Deux traces toujours affichées**, même en trame minimale : la ligne de qualification
  (étape 1) et le bloc feuille de route complet (étape 4, ligne Worktree comprise). Ce
  sont elles qui rendent le rituel vérifiable après coup.
- **Pas de porte** : présenter la feuille de route puis démarrer. S'arrêter seulement
  quand l'objectif reste flou après l'étape 2, ou devant une action irréversible.
- **Déclenchement automatique** : à chaque point de contrôle, annoncer en une ligne le
  skill lancé et pourquoi, puis le lancer. L'utilisateur interrompt quand il n'en veut pas.
- **Réentrance** : déjà ouvert dans la session ? Ne pas rejouer, traiter le delta
  (nouvel item, changement de périmètre) et mettre la feuille de route à jour.
- **Ne jamais inventer** : lire avant de supposer. Dans le doute, demander.
- **Skills absents** : un skill cité ici et non installé se remplace par le principe
  qu'il porte (écrire le test d'abord, reproduire le bug avant de corriger, etc.).
  Ne pas bloquer pour autant.
- Langue et ton : ceux de l'utilisateur et de son `CLAUDE.md`.

## Étape 0 : cadrer

1. **Projet** : le déduire de l'argument, du dossier courant et du `CLAUDE.md` global de
   l'utilisateur (qui indique souvent où vivent ses projets). Session ouverte ailleurs
   que dans le dossier du projet ? S'y déplacer avant toute lecture. Ambigu : demander
   lequel, rien d'autre.
2. **État git** : `status --porcelain`, branche courante, `fetch` puis écart avec la
   branche principale distante, puis `git worktree list` pour repérer les autres
   worktrees, donc les sessions possiblement actives. Travail non commité ou branche
   d'un autre chantier encore ouverte : le signaler et proposer la parade (commit, stash,
   finir l'autre d'abord) avant de créer quoi que ce soit. Dossier principal ailleurs que
   sur la branche principale, ou index contenant des fichiers stagés qui ne sont pas à
   toi : le signaler aussi, c'est le chantier d'une autre session. Ne pas y toucher, le
   worktree de l'étape 5 t'en isole. Pas de dépôt : le signaler, proposer `git init`
   (accord requis).
3. **Reprise du contexte**, dans cet ordre et sans tout lire : point de reprise laissé
   par une session précédente s'il en existe un, mémoire persistante, `CLAUDE.md` du
   projet, `CONTEXT.md` et `docs/adr/` s'ils existent, suivi de projets de l'utilisateur
   quand son `CLAUDE.md` en mentionne un, PR ouvertes (`gh pr list`). Lectures
   volumineuses : déléguer à un agent d'exploration avec un modèle léger fixé.
4. **Item du backlog** : retrouver sa formulation exacte là où vit le backlog du projet
   (fichier, issue, outil de gestion). Introuvable : demander à l'utilisateur de le coller.
5. **Pipeline de contrôle** : détecter `build`, `test`, `lint` (`package.json`, sinon
   `pytest`, `ruff`, `cargo`, `go test`...) et les lancer une fois, AVANT toute
   modification de fichier, puis citer le résultat dans la feuille de route : c'est la
   ligne de base. Rouge avant d'avoir touché quoi que ce soit : le dire maintenant, pas
   en fin de session.

## Étape 1 : qualifier

Trois axes, annoncés en une ligne : « Session [type], taille [x], sensibilité : [oui sur
quoi / non]. »

- **Type** : interface / logique métier / bug / base de données / refonte ou dette /
  mixte (nommer les parties).
- **Taille** : petit (moins d'une heure, 1 à 3 fichiers) / moyen (une demi-journée) /
  lourd (plusieurs jours, multi-domaines).
- **Sensibilité** : données personnelles, argent, authentification ou droits d'accès,
  secrets, envoi réel (email, SMS). Un seul oui suffit.

Lourd ? Proposer en une ligne de basculer en `bras-droit` ou de découper en plusieurs
sessions, puis continuer selon la réponse.

## Étape 2 : objectif vérifiable

1. Reformuler l'objectif en une phrase vérifiable. Pas « améliorer le formulaire », plutôt
   « le formulaire refuse un email invalide côté serveur et le test le prouve ».
2. Lister 2 à 5 critères d'acceptation, chacun avec la commande ou le geste qui le prouve
   (test, build, parcours dans le navigateur).
3. Dire ce qui est HORS périmètre, pour tenir la session sur un seul chantier.
4. Flou persistant : besoin mal défini, rédiger une spec courte avec l'utilisateur ;
   décision à éprouver, la soumettre à un interrogatoire contradictoire ; vocabulaire
   métier flou et projet sans `CONTEXT.md`, proposer d'en écrire un. C'est le seul cas où
   tu attends avant de démarrer.

## Étape 3 : prescrire

Choisir dans la table selon l'étape 1. Garder 3 à 5 skills, placés à leur moment. Les
noms sont indicatifs : prendre l'équivalent installé chez l'utilisateur.

| Situation | Avant de coder | Pendant | Avant la PR |
|---|---|---|---|
| Logique métier | modélisation du domaine quand le vocabulaire est flou, conception de module quand le module est neuf | `tdd` | |
| Interface | skill de design frontend, psychologie UX quand la conversion est en jeu | librairie d'animation du projet | passe de finition, audit d'accessibilité, revue de design |
| Bug | `diagnosing-bugs` ou équivalent : reproduire par un test d'abord | `tdd` pour le correctif | |
| Base de données | lecture du schéma et des conseils de sécurité de la base | migration testée en local ou sur une branche de base | conseils de sécurité relus |
| Refonte ou dette | conception de module, carte des dépendances | tests verts à chaque pas | |
| Rangement du dépôt (docs, scripts, assets, config) | aucun skill de dev ; `grep` des références à chaque fichier avant de le déplacer ou de le supprimer | pipeline vert à chaque pas | suppression = porte, accord de l'utilisateur |
| Plan moyen ou lourd | revue d'ingénierie du plan, avocat du diable sur le plan | | |
| Librairie ou API tierce | documentation à jour (serveur MCP de doc, site officiel) | | |
| Tout ce qui a un parcours utilisateur | | | voir la fonctionnalité marcher pour de vrai (navigateur, app lancée) |

**Sensibilité = oui** : `security-review` avant la PR, plus un contrôle RGPD quand des
données personnelles sont en jeu. Systématique, quelle que soit la taille : ces sujets
compilent, passent les tests et paraissent cohérents tout en étant faux.

**Ne pas prescrire ici** `/code-review`, la revue de réalité ni `/simplify` : ils
appartiennent à `cloture-session`. Les lancer deux fois est du gaspillage.

## Étape 4 : feuille de route

Afficher la trame de la session, compacte, puis démarrer sans attendre :

```
Session : [projet], [item]
Objectif : [phrase vérifiable]
Critères : [liste courte, avec la preuve de chacun]
Hors périmètre : [...]
Ligne de base : [build / test / lint : état avant modification]
Branche : [type/nom-court]
Worktree : [../projet-wt-nom, ou « dossier principal » quand il est sauté]

1. Avant de coder : [skills + raison en quelques mots]
2. Développement : [skills, découpage en pas vérifiables]
3. Avant la PR : [skills]
4. Fermeture : cloture-session
```

Hypothèses posées : les écrire sous la trame, une ligne chacune.

## Étape 5 : départ

1. Créer la branche dans un **worktree dédié**, pas dans le dossier principal (jamais de
   commit sur la branche principale), nommée selon les conventions du projet, à défaut
   `feat/`, `fix/`, `refactor/` plus un nom court :
   `git fetch && git worktree add -b <type/nom-court> ../<projet>-wt-<nom> origin/main`
   (adapter le nom de la branche principale). S'y déplacer, puis travailler et committer
   uniquement dans ce worktree. Raison : deux sessions dans le même dossier partagent le
   même index, `git commit` embarque tout l'index, et quelques secondes suffisent à
   périmer une vérification de branche. Session déjà ouverte dans un worktree (visible à
   l'étape 0) : ne pas en créer un second, y créer la branche.
2. Équiper le worktree : dépendances et fichiers d'environnement non versionnés. Sur
   macOS (APFS), `cp -Rc ../<projet>/node_modules node_modules` clone sans dupliquer
   réellement l'espace disque ; ailleurs, réinstaller. Pour `.env.local`, un lien
   symbolique vers le dossier principal suffit. Éviter le lien symbolique sur
   `node_modules` : certains bundlers (Turbopack notamment) refusent un `node_modules`
   qui pointe hors de la racine du projet.
3. Proportionné : projet sans sessions parallèles ni hooks lourds, le worktree reste
   recommandé et peut être sauté en l'annonçant en une ligne. La branche se crée alors
   depuis la branche principale à jour, dans le dossier principal.
4. Le worktree se retire après le merge (`git worktree remove`) : c'est `cloture-session`
   qui s'en charge. Au merge, `gh pr merge --delete-branch` ne supprime pas la branche
   locale tant que le worktree la tient : retirer le worktree d'abord, supprimer la
   branche ensuite.
5. Lancer le premier skill de la phase 1 et enchaîner.

## Pendant la session : tenir la trame

- À chaque changement de phase, annoncer en une ligne « Point de contrôle : je lance
  [skill] parce que [raison] » et le lancer.
- Avancer par pas vérifiables : un pas, sa preuve (test, build, écran), puis le suivant.
- Tout test écrit doit avoir été vu ROUGE une fois avant de passer au vert.
- Le périmètre dérive (nouvelle demande, découverte en route) ? Le dire : l'intégrer et
  mettre à jour la feuille de route, ou le noter pour une autre session. Un défaut hors
  périmètre repéré au passage se signale, il ne se corrige pas dans cette branche.
- Contexte qui gonfle ou fin d'un gros pas : écrire un point de reprise (fichier de
  passation dans le projet, ou skill dédié quand il existe) et proposer de continuer
  dans une session neuve.
- Critères d'acceptation tous prouvés : commit, push, PR (jamais de merge sans accord
  explicite), puis proposer `cloture-session`.
