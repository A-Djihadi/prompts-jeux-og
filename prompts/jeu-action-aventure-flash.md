# Prompt — Jeu d'action-aventure style Flash (vectoriel, sans sprites)

> Copie tout ce qui est sous la ligne et colle-le dans ton agent IA.
> Remplace les `[...]` si tu veux personnaliser, sinon laisse les valeurs par défaut.

---

## Rôle et objectif

Tu es un développeur de jeux vidéo expérimenté, spécialisé en game feel et en jeux
d'action. Construis un **jeu d'action-aventure jouable dans le navigateur, dans
l'esprit des jeux Flash** : graphismes **vectoriels** (formes, contours, dégradés)
dessinés par le code, **sans aucun sprite ni image**, combat nerveux, exploration de
salles, et une progression qui donne envie de pousser « encore une salle ».

« Addictif » ici veut dire : combat qui répond parfaitement, échec rapide et pas
frustrant, progrès visibles, récompenses régulières **dans le jeu**. Tu n'utilises
aucune technique qui exploite le joueur (voir « Garde-fous »).

## Paramètres (à personnaliser)

- **Nom du jeu :** [NOM DU JEU — par défaut : choisis-en un]
- **Vue :** [vue de dessus | vue de côté — par défaut : vue de dessus]
- **Thème / univers :** [THÈME — par défaut : choisis un thème original]
- **Style visuel :** [formes géométriques + contours épais + couleurs vives — par défaut]
- **Héros et arme principale :** [par défaut : choisis, ex. épée + dash]
- **Langue des textes du jeu :** [français]

## Stack technique (obligatoire)

- **HTML5 Canvas 2D + JavaScript vanilla.** Aucun framework, aucune librairie,
  aucun moteur, aucun CDN, aucune dépendance réseau. Le jeu fonctionne hors ligne.
- Fichiers : `index.html`, `style.css`, et le JS en modules ES (`src/*.js`).
- Lancement : en ouvrant `index.html` via un petit serveur statique
  (ex. `npx serve .` ou `python -m http.server`) — documente la commande.
- **Zéro sprite, zéro image** : tout est dessiné avec l'API Canvas (`path`, `arc`,
  `lineTo`, `bezierCurveTo`, dégradés, `globalAlpha`, transformations). Chaque
  entité a une fonction `draw(ctx)` qui la dessine en formes vectorielles. Les
  animations passent par des transformations (rotation, échelle, offset) et des
  tweens, pas par des frames.
- Le canvas s'adapte à la fenêtre et reste net (prends en compte `devicePixelRatio`).
- **Sons générés avec la Web Audio API** (oscillateurs, bruit, enveloppes) : pas de
  fichiers audio requis. L'audio démarre après la première interaction.
- Boucle de jeu avec `requestAnimationFrame` et **pas de temps fixe** pour la
  simulation (accumulateur), rendu découplé.
- Toutes les valeurs de réglage (vitesses, dégâts, fenêtres de timing, intensité
  des effets, taux de drop…) sont dans **un seul objet `CONFIG`**, commenté.

## Architecture attendue

Sépare clairement deux couches :

1. **Simulation** : entrées, déplacements, collisions, combat, IA, règles, score.
   Elle ne dessine rien.
2. **Rendu et effets (« polish »)** : formes, particules, shake, flash, sons.
   Les effets ne modifient jamais la simulation. Si on coupe tous les effets,
   le jeu se joue exactement pareil.

Modules suggérés : `input`, `config`, `shapes` (primitives de dessin), `audio`,
`entities`, `combat`, `rooms`, `effects`, `camera`, `ui`, `save`, `main`.

## Mécaniques à implémenter (avec le principe derrière chacune)

Implémente **toutes** ces mécaniques. Le tag `[Px]` renvoie au principe de game
design correspondant (détaillé et sourcé dans `references/game-design.md` du repo
d'où vient ce prompt ; tu n'as pas besoin de ce fichier pour coder).

### 1. Le game feel d'abord `[P1]`
- Chaque input produit une réponse visible et audible **dans la même frame**.
- Déplacement avec accélération et décélération réglables.
- Une action d'esquive (dash/roulade) avec une courte invulnérabilité, réglable.

### 2. Pardonner le timing `[P4]`
- **Buffer d'input** : une attaque ou une esquive pressée juste avant la fin de
  l'action en cours est exécutée dès que possible, au lieu d'être perdue.
- Hitbox du héros un peu plus petite que sa forme dessinée ; hitbox des bonus et
  des attaques du héros un peu généreuses.
- Si vue de côté : coyote time, jump buffer et correction de coin en plus.
- Toutes ces fenêtres sont des constantes dans `CONFIG`.

### 3. Le juice du combat, effet par effet `[P2]` `[P3]`
Chaque effet est un **booléen dans `CONFIG.juice`** avec son intensité réglable :
- **Hit stop** : la simulation se fige un très court instant quand un coup porte.
- **Screen shake** court sur les impacts, plus fort sur les coups lourds et le boss.
- **Knockback** : les ennemis touchés sont repoussés ; léger recul du héros sur
  ses propres attaques.
- Flash blanc de la forme touchée, puis retour à la normale.
- Traînée vectorielle (arc) sur les attaques ; muzzle flash si arme à distance ;
  projectiles gros et lisibles.
- Squash & stretch des formes à l'impact, au dash, à l'apparition.
- Particules (éclats, étincelles) à chaque coup, mort d'ennemi, collecte.
- Nombres de dégâts ou de score qui « popent » avec un tween.
- Un son distinct pour chaque événement important (coup, coup reçu, dash, bonus,
  mort, porte ouverte, boss). Le son est l'effet le plus rentable : soigne-le.
- Caméra qui suit le héros en douceur et anticipe un peu la direction du mouvement.
- Un **panneau de debug** (touche `Tab`) qui permet d'activer/désactiver chaque
  effet en direct, pour comparer le jeu avec et sans juice.

### 4. Échec rapide, retour immédiat `[P5]`
- À la mort, **retour quasi instantané** au début de la salle ou au dernier checkpoint.
- Relancer une partie = **une seule touche** depuis l'écran de game over.
- Salles **courtes**, avec l'objectif de la salle clair (vaincre les ennemis,
  trouver la clé, atteindre la sortie).
- Transitions entre salles rapides.
- Fin de zone / fin de run : récap (temps, ennemis vaincus, morts, objets trouvés)
  qui montre la progression.

### 5. Flow : défi progressif, objectifs clairs `[P6]` `[P10]`
- La première zone sert de tutoriel **intégré au gameplay** : déplacement, attaque,
  esquive, chacun introduit dans une situation sans danger.
- Monde en **[3] zones** de plusieurs salles, chacune avec un **boss**. Chaque zone
  introduit **un** élément nouveau (type d'ennemi, piège, capacité du héros),
  d'abord isolé, puis combiné avec ce qu'on connaît.
- Ennemis aux comportements lisibles `[P14]` : chaque attaque ennemie est
  **annoncée** (télégraphiée) par un court délai et un signal visuel et/ou sonore
  avant de frapper, pour que chaque coup reçu paraisse juste.
- L'objectif en cours est toujours visible (HUD + mini-carte des salles visitées).
- Clés, portes verrouillées et au moins **une capacité qui ouvre de nouveaux
  passages** dans des zones déjà visitées (esprit aventure).

### 6. Difficulté dynamique discrète `[P7]`
- Compte les morts consécutives dans une même salle. Au-delà d'un seuil
  (`CONFIG`), aide **discrètement** (ex. un peu plus de soins lâchés, un ennemi en
  moins dans la vague).
- L'aide ne change jamais les règles de façon visible et peut être désactivée
  dans les options.
- Propose aussi un choix manuel de difficulté modifiable à tout moment.

### 7. Récompenses variables + paliers fixes `[P8]`
- Les ennemis vaincus ont une **chance** (pas une certitude) de lâcher des pièces,
  du soin ou un power-up temporaire. Probabilités dans `CONFIG`.
- En parallèle, des **paliers fixes** : boss vaincu = nouvelle capacité ou
  amélioration garantie ; coffres cachés à trouver dans chaque zone.
- Les récompenses ne baissent jamais brutalement d'une zone à l'autre.

### 8. Compétence et autonomie `[P9]`
- Contrôles simples : [flèches/WASD + attaque + esquive (+ action secondaire)].
- **Choix d'amélioration** : après chaque boss (et/ou dans une boutique avec les
  pièces gagnées en jeu), le joueur choisit **1 amélioration parmi 3**
  (ex. dégâts, portée, dash plus long, soin), pour créer son propre style.
- Score, meilleur score, meilleur temps et progression **sauvegardés** en `localStorage`.
- Écran de fin de run avec stats et records battus.

## Garde-fous (obligatoires) `[P12]`

Le jeu ne contient **pas** : d'argent réel ni de monnaie premium, de loot box, de
récompense quotidienne ou de timer qui oblige à revenir à une heure précise, de
grind répétitif pour débloquer la suite, de fausses notifications sociales.
Le joueur revient parce que le jeu est bon.

## Accessibilité de base `[P13]`

- Pause à tout moment (`Échap` ou `P`).
- Touches remappables, réglages sauvegardés en `localStorage`.
- Option « réduire les effets » qui coupe le screen shake, les flashs et les
  mouvements d'arrière-plan.
- Volumes séparés musique / effets, et un bouton mute.
- Rappel des contrôles et de l'objectif accessible en jeu.
- On lance une partie en une touche depuis l'écran titre.

## Démarche demandée

Avance dans cet ordre et ne passe à l'étape suivante que quand la précédente marche :

1. **Mini document de design** (`GAME_DESIGN.md`, 1 page) `[P11]` : les émotions
   visées (ex. puissance, tension, découverte), la boucle de jeu principale (salle →
   combat → récompense → salle suivante), la carte des zones et l'élément nouveau
   de chacune, et un tableau « mécanique → principe `[Px]` → où dans le code ».
2. **Prototype sans effets** : déplacement, attaque, esquive, un type d'ennemi,
   une salle. Vérifie que c'est déjà agréable à contrôler.
3. **Forgiveness** (section 2), puis réglage des constantes.
4. **Juice du combat** (section 3), effet par effet, chacun avec son interrupteur.
5. **Contenu** : salles, zones, ennemis, boss, clés et portes.
6. **Progression, récompenses, améliorations, sauvegarde** (sections 5 à 8).
7. **Options et accessibilité.**
8. **Auto-vérification** avec la checklist ci-dessous ; corrige ce qui ne passe pas.

## Critères de qualité (checklist à valider avant de rendre)

- [ ] Le jeu se lance sans erreur dans la console, dans un navigateur récent.
- [ ] Aucune dépendance externe, aucun appel réseau, **aucun fichier image**.
- [ ] Toutes les valeurs de réglage sont dans `CONFIG`, commentées.
- [ ] La simulation tourne à pas fixe ; le jeu ne va pas plus vite sur un écran à
      haute fréquence de rafraîchissement.
- [ ] Désactiver tous les effets via le panneau `Tab` ne change pas le gameplay.
- [ ] Hit stop, screen shake, knockback et flash d'impact sont présents et réglables.
- [ ] Chaque attaque ennemie est télégraphiée avant de frapper.
- [ ] Le buffer d'input fonctionne (décris comment le tester).
- [ ] Mort → retour quasi instantané ; game over → relance en une touche.
- [ ] Chaque zone introduit un élément nouveau ; au moins une capacité rouvre des
      passages dans une zone déjà visitée.
- [ ] Progression, records et réglages persistent après rechargement de la page.
- [ ] Le rendu reste net quelle que soit la taille de la fenêtre.
- [ ] Pause, remapping, « réduire les effets », volumes séparés : présents et fonctionnels.
- [ ] Aucun des éléments interdits dans « Garde-fous ».

## Livrables attendus

1. Le code complet du jeu (`index.html`, `style.css`, `src/`).
2. `README.md` du jeu : comment le lancer, contrôles, options, comment ouvrir le
   panneau de debug.
3. `GAME_DESIGN.md` : le mini document de design de l'étape 1, à jour.
4. Un court rapport final : ce qui est fait, ce qui reste à améliorer, et le
   résultat de chaque point de la checklist (coché ou non, avec explication).
