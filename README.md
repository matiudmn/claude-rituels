# Rituels pour Claude Code

Trois rituels en français pour cadrer le travail avec Claude Code, du premier message à la fermeture de la session.

| Rituel | Quand | Ce qu'il fait |
|---|---|---|
| `ouverture-session` | Au démarrage d'un chantier de code | Cadre le projet et l'état git, reprend le contexte, fixe un objectif vérifiable, prescrit 3 à 5 skills adaptés, ouvre un worktree dédié et affiche la feuille de route |
| `cloture-session` | Avant de fermer la session | État des lieux, passes qualité adverses, scan des secrets avant tout commit, git, retrait du worktree, mise à jour de la mémoire |
| `bras-droit` | Quand vous déléguez un chantier entier | Claude pilote comme un directeur technique : il découpe, délègue à des sous-agents, vérifie par la preuve et vous réserve les décisions irréversibles |

Deux agents accompagnent le mode bras droit :

- `bd-executant` : implémente un lot borné, sans committer ni franchir de porte.
- `bd-verificateur` : contrôle un lot livré à contexte vierge, par la preuve, sans rien modifier.

## Pourquoi ces rituels

Je suis Matthieu Daumain, consultant en visibilité Google & IA. Je code une bonne partie de mes outils avec Claude Code, et ces rituels sont nés de mes sessions : une session = un chantier, un objectif vérifiable, et une fermeture propre.

Trois principes les traversent :

- **La preuve avant le « c'est fait »** : un test vu rouge puis vert, un build, une capture. Une affirmation d'agent n'est pas une preuve.
- **Des portes claires** : Claude décide seul des micro-choix et vous garde les actions irréversibles (suppression, merge, déploiement, envoi, publication, dépense).
- **La sobriété en tokens** : peu de sous-agents, un modèle fixé à chaque appel, une seule vérification par lot.

## Installation

Dans Claude Code :

```
/plugin marketplace add MatiuDmn/claude-rituels
/plugin install rituels@claude-rituels
```

Les rituels se lancent ensuite par leur nom (`/rituels:ouverture-session`, `/rituels:cloture-session`, `/rituels:bras-droit`) ou se déclenchent seuls quand vous dites « on ouvre une session », « on clôture » ou « prends le lead ».

## Adapter à votre environnement

Les rituels ne supposent aucun outil en particulier. Ils lisent votre `CLAUDE.md` global pour savoir où vivent vos affaires. Pour qu'ils en tirent le meilleur, indiquez-y :

- le dossier où se trouvent vos projets ;
- votre suivi de projets, quand vous en tenez un (fichier, tableau, outil) ;
- votre base de notes, quand vous en avez une (Obsidian, Notion...) ;
- le dossier de sauvegarde des fichiers non versionnés ;
- votre politique git (par exemple : confirmer avant chaque commit, ou jamais de merge sans accord).

Vos règles priment toujours sur celles des rituels.

## Skills cités

Les rituels citent des skills pour chaque type de travail (tests d'abord, diagnostic de bug, design frontend, revue de sécurité...). Quand l'un d'eux manque chez vous, Claude applique le principe qu'il porte et continue. `/code-review`, `/simplify` et `security-review` sont fournis avec Claude Code.

## Licence

MIT. Reprenez, adaptez, améliorez. Un retour sur ce qui marche chez vous me fera plaisir.
