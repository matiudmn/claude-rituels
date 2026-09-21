---
name: cloture-session
description: Rituel de clôture de session (une session = un chantier) avant de la fermer. Fait l'état des lieux, enchaîne les contrôles qualité du code (code-review, revue de réalité, simplify), scanne secrets et données personnelles avant tout commit, fait le point git (commit, push, sauvegarde, retrait du worktree), met à jour la mémoire et le suivi de projets, puis confirme la fermeture. Utiliser quand l'utilisateur termine une session et veut fermer proprement, dit « on clôture », « on ferme la session », « fin de session », « la session est finie ». Symétrique de ouverture-session.
argument-hint: [contexte éventuel]
---

# Clôture de session

Une session = un chantier. Fermer proprement : code quasi parfait, rien d'oublié,
secrets jamais poussés, mémoire à jour. Argument éventuel : `$ARGUMENTS`.

## Règles permanentes

- **Porte souple** : signaler et chiffrer le risque, jamais bloquer. L'utilisateur décide.
- **Adapter au type** : les passes qualité (étape 2) ne se déclenchent QUE sur du code.
- **Court-circuit** : aucun projet de code touché et rien de mémorable ? Annoncer
  « session sans impact code ni mémoire » et sauter directement à l'étape 6.
- **Réentrance** : déjà passé dans la session ? Repartir de l'état atteint, traiter le
  delta, ne pas rejouer.
- **Ne jamais inventer** : reconstituer le travail réel. Dans le doute, demander.
- **Skills absents** : un skill cité ici et non installé se remplace par le principe
  qu'il porte. Ne pas bloquer pour autant.
- Langue et ton : ceux de l'utilisateur et de son `CLAUDE.md`.

## Étape 0 : cadrer

1. **Projets touchés** : les déduire des chemins de fichiers réellement lus, édités ou
   créés pendant la session, remontés à la racine de leur dépôt. Demander à
   l'utilisateur en dernier recours. Session menée dans un worktree dédié
   (`git worktree list`) : c'est lui le dossier de travail pour toutes les étapes git,
   pas le dossier principal.
2. **Versionné ?** `git -C <projet> rev-parse --git-dir` puis `status --porcelain`.
   Distinguer « pas de dépôt » (étape 3 point 1) de « rien à committer ».
3. **Type** : code (étape 2 active) / contenu ou administration (sauter l'étape 2 et
   l'annoncer) / mixte (étape 2 sur la seule partie code).
4. Annoncer : « Session [type], projet(s) [x]. »

## Étape 1 : état des lieux

1. Comparer le travail réel à l'objectif : ce qui est fini, ce qui reste, les « je
   finirai plus tard », le code à moitié fait, les TODO et FIXME ajoutés, les fichiers
   temporaires laissés.
2. Lancer les contrôles du projet (cwd = le projet) et montrer la sortie brute.
   Détecter le pipeline : `package.json` (`build`, `test`, `lint`), sinon l'outillage
   du langage (`pytest`, `ruff`, `cargo`, `go test`...). Aucun pipeline détecté ?
   L'annoncer, ne pas lancer de commande à l'aveugle.
3. **Tout test écrit ou modifié dans la session doit avoir été vu ROUGE une fois.**
   Saboter ce qu'il prétend garder (le motif le plus INDIRECT, pas le cas évident),
   constater l'échec, restaurer depuis une copie hors git (jamais `git checkout`, qui
   écraserait aussi le travail non commité), constater le vert. Un test qui ne devient
   jamais rouge donne une fausse couverture, pire que pas de test. Pas fait pendant la
   session ? Le faire ici.
4. Lister les points d'attention. Blocage net : le dire, ne rien maquiller.

## Étape 2 : passes qualité (code uniquement, automatique)

Pas de code ? Sauter l'étape et l'annoncer. Sinon enchaîner les trois, dans cet ordre,
et restituer chaque résultat.

**Pas de double passage.** Une passe déjà faite dans cette session (par exemple en fin de
`bras-droit`) ne se relance pas sur le même code : la refaire seulement sur le delta,
c'est-à-dire les modifications postérieures à ce passage. Aucun delta : l'annoncer en une
ligne et passer. Aucune trace certaine dans la session : faire la passe complète. Dans le
doute, faire la passe.

1. **`/code-review`** sur le diff de la session, effort proportionné à sa taille.
   **Exception : données personnelles, conformité, argent ou sécurité, là l'effort ne se
   module pas.** Une ligne y suffit à créer un défaut, et ces sujets compilent, passent
   les tests et paraissent cohérents tout en étant faux. Constats classés par gravité.
2. **Revue de réalité** : un agent à contexte vierge (l'agent `bd-verificateur` fourni
   avec ces rituels, ou équivalent) établit l'état RÉEL : ce qui marche de bout en bout
   contre ce qui est annoncé fait, plus la liste franche de ce qui reste. Quand
   `bd-verificateur` a déjà contrôlé les lots par la preuve et que rien n'a changé
   depuis, reprendre son rapport au lieu d'en relancer un.
3. **`/simplify`** : réutilisation, simplification, efficacité, altitude. Montrer le
   diff appliqué. Il ne cherche pas les bugs, c'est le rôle de `/code-review`.

**Ces passes sont ADVERSES.** Les briefer pour réfuter le travail, pas le valider :
exiger le scénario d'échec concret, pas un avis. Deux passes indépendantes qui
convergent = un fait. Vérifier soi-même dans le code chaque constat avant de le relayer
ou de le corriger : un agent se trompe, et un constat relayé sans preuve devient une
fausse certitude.

**Défaut à chercher explicitement** : la garde annoncée et absente. L'écran affiche un
avertissement, une confirmation ou une case à cocher, et le serveur ne revérifie rien,
ou un chemin voisin contourne (assistant IA, route API, tâche planifiée). Compter les cas
que l'interface distingue, les comparer à ceux que la règle partagée porte : toute
différence est un garde-fou qui n'existe pas.

`/simplify` modifie le code en dernier, sans nouvelle revue derrière : **relancer les
contrôles de l'étape 1**. Rouge ? Le signaler, laisser l'utilisateur trancher.

## Étape 3 : git et sécurité (avant TOUT commit, par projet)

1. **Non versionné** : le signaler, proposer `git init` (accord requis) ou, à défaut,
   scanner puis sauvegarder (point 5) plutôt que committer.
2. **Scan secrets et données personnelles** sur ce qui PARTIRAIT au commit : fichiers
   modifiés suivis ET non suivis (`status --porcelain`, l'index est encore vide).
   Scanner le **contenu**, pas la taille : un secret poussé est compromis, dépôt privé ou
   non. Cibles :
   - secrets : clés d'API, tokens, JWT, chaînes à forte entropie. Motifs courants, non
     exhaustifs : `sk-`, `ghp_`, `xkeysib-`, `AKIA`, `Bearer eyJ...`. Utiliser `gitleaks`
     ou `trufflehog` quand ils sont installés ;
   - fichiers à risque : tout `.env`, exports d'outils no-code (les scénarios exportés
     embarquent souvent des clés en clair), exports de fichiers partagés, listes de
     contacts ou de diffusion, dossiers contenant des données personnelles ;
   - tout fichier de plus de 100 Mo (refusé par GitHub).
   **Une occurrence = STOP** : la montrer, ne rien committer, proposer la parade
   (`.gitignore`, placeholder du type `__API_KEY__` dans les fichiers gardés en
   documentation, rotation de la clé quand elle a déjà fuité).
3. **État git** : `status` plus un résumé du diff non commité.
4. **Commit et push** : stager des fichiers **précis** (jamais `git add -A`, relire
   `git diff --cached`), message dans la langue du projet, jamais `--force` ni
   `--no-verify` sans accord. Suivre la politique de l'utilisateur (son `CLAUDE.md`) sur
   ce qui demande confirmation ; à défaut, demander avant de committer et avant de
   pousser.
5. **Sauvegarde** des fichiers utiles restés non versionnés, dans le dossier que
   l'utilisateur désigne (son `CLAUDE.md` en indique souvent un), ou sur demande.
6. **Worktree** (ouvert par `ouverture-session`) : PR mergée, et seulement alors,
   `git worktree remove ../<projet>-wt-<nom>` depuis le dossier principal, puis
   `git branch -d <branche>`. Refus de `remove` = travail non commité : le montrer,
   jamais `--force` sans accord. Refus de `branch -d` après un squash-merge : vérifier
   que `gh pr view <branche>` indique MERGED avant `-D`. PR pas encore mergée : laisser
   le worktree en place et le porter aux points ouverts.

## Étape 4 : mémoire et traçabilité

1. Relever ce qui mérite d'être **mémorisé durablement** : décisions, préférences, état
   d'avancement, échéances, process, tout ce qui ne se déduit pas du code ou de git.
   Dates relatives converties en dates absolues.
2. Chercher **d'abord** une mémoire existante à compléter (pas de doublon). Sinon en
   créer une au format des voisines. Supprimer une mémoire devenue fausse.
3. **Suivi de projets** : quand l'utilisateur tient un index de ses projets (fichier,
   tableau, outil nommé dans son `CLAUDE.md`), le mettre à jour quand la session a créé
   un projet, poussé du code, supprimé quelque chose ou changé un état de sauvegarde.
4. **Base de notes** : quand l'utilisateur en tient une (Obsidian, Notion...) et qu'un de
   ses projets y a avancé, proposer la mise à jour selon ses conventions. N'y recopier
   aucune donnée sensible. Rien à voir avec ses notes ? Sauter sans bruit.
5. Présenter les mises à jour envisagées, écrire après l'accord. Ne jamais inventer une
   décision : dans le doute, demander.

## Étape 5 : suite métier (optionnelle)

L'utilisateur a un rituel dédié à la fin d'un chantier client (facturation, compte rendu,
CRM) ? Le proposer quand le chantier s'y prête. Rien de tel ? Sauter sans bruit : ce skill
ne facture pas et n'écrit dans aucun outil tiers.

## Étape 6 : récap et fermeture

D'abord, **relire les traces écrites plus tôt dans la session et corriger celles que la
suite a rendues fausses** (« PR ouverte » après un merge, « à faire » après livraison,
un décompte périmé). Une trace fausse sera lue comme vraie : elle coûte plus cher que
pas de trace.

Puis un récap **compact**, une ligne par poste, en sautant les postes hors sujet (pas de
« N/A ») : session (type, projets) ; qualité (les 3 passes, build et tests) ; git et
sécurité (scan, commité, poussé) ; mémoire (entrées, suivi de projets, notes) ;
**points ouverts** avec leur risque, ou « rien en suspens ».

Enfin demander : « Je ferme la session ? » Rappeler une dernière fois les points
ouverts avant que l'utilisateur tranche. Après son accord, clôturer par une ligne
marqueur, par exemple « Session clôturée : [projet], [date]. Qualité OK, secrets OK,
mémoire à jour. »
