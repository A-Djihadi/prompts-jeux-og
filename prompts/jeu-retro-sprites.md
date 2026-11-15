# Prompt : jeu rétro pixel art (basé sprites)

> Copie tout ce qui est sous la ligne et colle-le dans ton agent IA.
> Remplace les `[...]` si tu veux personnaliser, sinon laisse les valeurs par défaut.

---

## Rôle et objectif

Tu es un développeur de jeux vidéo expérimenté, spécialisé en game feel et en jeux
arcade rétro. Construis un **jeu rétro en pixel art, jouable dans le navigateur**,
qui donne envie de relancer une partie dès qu'on perd.

« Addictif » ici veut dire : contrôles qui répondent parfaitement, échec rapide et
pas frustrant, progrès visibles, récompenses régulières **dans le jeu**. Tu
n'utilises aucune technique qui exploite le joueur (voir « Garde-fous »).

## Paramètres (à personnaliser)

- **Nom du jeu :** [NOM DU JEU, par défaut : choisis-en un]
- **Genre :** [plateforme | shoot'em up vertical | arcade à l'écran unique, par défaut : plateforme]
- **Thème / univers :** [THÈME, par défaut : choisis un thème original]
- **Palette :** [palette limitée de 8 à 16 couleurs, par défaut : crée-la et documente-la]
- **Résolution interne :** [par défaut : 320x180, agrandie à l'écran en pixels nets]
- **Langue des textes du jeu :** [français]

## Stack technique (obligatoire)

- **HTML5 Canvas 2D + JavaScript vanilla.** Aucun framework, aucune librairie,
  aucun moteur, aucun CDN, aucune dépendance réseau. Le jeu fonctionne hors ligne.
- Fichiers : `index.html`, `style.css`, et le JS en modules ES (`src/*.js`) ou un
  seul `game.js` si le jeu est petit.
- Lancement : en ouvrant `index.html` via un petit serveur statique
  (ex. `npx serve .` ou `python -m http.server`). Documente la commande.
- **Sprites en pixel art générés par le code** : chaque sprite est défini comme une
  grille de caractères (une lettre = une couleur de la palette), convertie au
  démarrage en canvas hors écran. Animations = plusieurs frames par sprite.
  Pas de fichiers image externes requis.
- Rendu à la résolution interne sur un canvas, puis agrandi à l'écran avec un
  facteur entier et `imageSmoothingEnabled = false` pour garder des pixels nets.
- **Sons générés avec la Web Audio API** (oscillateurs carrés/triangles, bruit) :
  pas de fichiers audio requis. L'audio démarre après la première interaction.
- Boucle de jeu avec `requestAnimationFrame` et **pas de temps fixe** pour la
  simulation (accumulateur), rendu découplé.
- Toutes les valeurs de réglage (vitesses, gravité, fenêtres de timing, intensité
  des effets, taux de drop…) sont dans **un seul objet `CONFIG`**, commenté.

## Architecture attendue

Sépare clairement deux couches :

1. **Simulation** : entrées, physique, collisions, règles, score. Elle ne dessine rien.
2. **Rendu et effets (« polish »)** : sprites, particules, shake, flash, sons.
   Les effets ne modifient jamais la simulation. Si on coupe tous les effets,
   le jeu se joue exactement pareil.

Modules suggérés : `input`, `config`, `sprites`, `audio`, `entities`, `level`,
`effects`, `ui`, `save`, `main`.

## Mécaniques à implémenter (avec le principe derrière chacune)

Implémente **toutes** ces mécaniques. Le tag `[Px]` renvoie au principe de game
design correspondant (détaillé et sourcé dans `references/game-design.md` du repo
d'où vient ce prompt ; tu n'as pas besoin de ce fichier pour coder).

### 1. Le game feel d'abord `[P1]`
- Chaque input produit une réponse visible et audible **dans la même frame**.
- Accélération et décélération réglables (pas de mouvement « on/off » rigide).
- Saut à hauteur variable : relâcher le bouton plus tôt coupe la montée.

### 2. Pardonner le timing `[P4]` (inspiré de Celeste)
- **Coyote time** : le saut reste possible un court instant après avoir quitté un rebord.
- **Jump buffer** : un saut pressé juste avant d'atterrir part à l'atterrissage.
- **Gravité réduite au sommet du saut** si le bouton reste enfoncé.
- **Correction de coin** : si la tête accroche un coin de quelques pixels, décale
  le joueur pour qu'il passe.
- Hitbox du joueur un peu plus petite que son sprite ; hitbox des bonus un peu plus grande.
- Toutes ces fenêtres sont des constantes dans `CONFIG`.
- (Si tu as choisi le shoot'em up : buffer du tir et hitbox du vaisseau réduite au
  cœur du sprite.)

### 3. Le juice, effet par effet `[P2]` `[P3]`
Chaque effet est un **booléen dans `CONFIG.juice`** avec son intensité réglable :
- Flash blanc du sprite touché, puis retour à la normale.
- Squash & stretch au saut et à l'atterrissage.
- Particules : poussière à l'atterrissage, éclats à la destruction d'un ennemi,
  étincelles à la collecte d'un bonus.
- Screen shake court sur les impacts forts (dégâts, explosion, boss).
- Hit stop : la simulation se fige un très court instant quand un coup porte.
- Tweening/easing sur l'UI (score qui « pop », textes qui rebondissent).
- Un son distinct pour chaque événement important (saut, coup, bonus, mort,
  niveau terminé). Le son est l'effet le plus rentable : soigne-le.
- Un **panneau de debug** (touche `Tab`) qui permet d'activer/désactiver chaque
  effet en direct, pour comparer le jeu avec et sans juice.

### 4. Échec rapide, retour immédiat `[P5]` (inspiré de Super Meat Boy)
- Pas de système de vies limitées qui renvoie au menu : à la mort, **respawn quasi
  instantané** au début du niveau ou au dernier checkpoint.
- Relancer une partie = **une seule touche** depuis l'écran de game over.
- Niveaux **courts**, avec l'objectif visible ou clairement indiqué.
- Transitions entre niveaux rapides (pas de longues animations bloquantes).
- Fin de niveau : petit récap (temps, nombre de morts, pièces) qui montre la progression.

### 5. Flow : défi progressif, objectifs clairs `[P6]` `[P10]`
- Le premier niveau sert de tutoriel **intégré au gameplay** (pas d'écran de texte
  long) : chaque contrôle est introduit dans une situation sans danger.
- Chaque nouveau niveau introduit **un seul** élément nouveau (ennemi, obstacle,
  capacité), d'abord isolé, puis combiné avec ce qu'on connaît déjà.
- L'objectif en cours est toujours visible (HUD ou indication dans le décor).
- Minimum **[8] niveaux** + un boss final, avec une difficulté qui monte progressivement.

### 6. Difficulté dynamique discrète `[P7]`
- Compte les morts consécutives sur un même niveau. Au-delà d'un seuil (`CONFIG`),
  aide **discrètement** (ex. un checkpoint en plus, un ennemi un peu plus lent).
- L'aide ne change jamais les règles de façon visible et peut être désactivée
  dans les options.
- Propose aussi un choix manuel de difficulté modifiable à tout moment.

### 7. Récompenses variables + paliers fixes `[P8]`
- Les ennemis vaincus ont une **chance** (pas une certitude) de lâcher une pièce,
  un bonus ou un power-up temporaire. Probabilités dans `CONFIG`.
- En parallèle, des **paliers fixes** : fin de niveau, boss, objets cachés à trouver
  (ex. [3] pièces spéciales par niveau).
- Les récompenses ne baissent jamais brutalement d'un niveau à l'autre.

### 8. Compétence et autonomie `[P9]`
- Contrôles simples : [flèches/WASD + 1 ou 2 boutons d'action].
- Score, meilleur score et meilleur temps par niveau, **sauvegardés** en `localStorage`.
- Un vrai choix pour le joueur : par exemple choisir **1 amélioration parmi 3** à
  certains moments, ou des chemins alternatifs dans les niveaux.
- Écran de fin de run avec stats (temps, morts, pièces, records battus).

## Garde-fous (obligatoires) `[P12]`

Le jeu ne contient **pas** : d'argent réel ni de monnaie premium, de loot box, de
récompense quotidienne ou de timer qui oblige à revenir à une heure précise, de
grind répétitif pour débloquer la suite, de fausses notifications sociales.
Le joueur revient parce que le jeu est bon.

## Accessibilité de base `[P13]`

- Pause à tout moment (`Échap` ou `P`).
- Touches remappables, réglages sauvegardés en `localStorage`.
- Option « réduire les effets » qui coupe le screen shake et les flashs.
- Volumes séparés musique / effets, et un bouton mute.
- Rappel des contrôles accessible en jeu.
- On lance une partie en une touche depuis l'écran titre.

## Démarche demandée

Avance dans cet ordre et ne passe à l'étape suivante que quand la précédente marche :

1. **Mini document de design** (`GAME_DESIGN.md`, 1 page) `[P11]` : les émotions
   visées (ex. tension, maîtrise, surprise), la boucle de jeu principale, la liste
   des niveaux et l'élément nouveau de chacun, et un tableau
   « mécanique → principe `[Px]` → où dans le code ».
2. **Prototype sans effets** : déplacement, saut, collisions, un niveau. Vérifie
   que c'est déjà agréable à contrôler.
3. **Forgiveness** (section 2), puis réglage des constantes.
4. **Juice** (section 3), effet par effet, chacun avec son interrupteur.
5. **Contenu** : tous les niveaux, ennemis, boss.
6. **Progression, récompenses, sauvegarde** (sections 5 à 8).
7. **Options et accessibilité.**
8. **Auto-vérification** avec la checklist ci-dessous ; corrige ce qui ne passe pas.

## Critères de qualité (checklist à valider avant de rendre)

- [ ] Le jeu se lance sans erreur dans la console, dans un navigateur récent.
- [ ] Aucune dépendance externe, aucun appel réseau.
- [ ] Toutes les valeurs de réglage sont dans `CONFIG`, commentées.
- [ ] La simulation tourne à pas fixe ; le jeu ne va pas plus vite sur un écran à
      haute fréquence de rafraîchissement.
- [ ] Désactiver tous les effets via le panneau `Tab` ne change pas le gameplay.
- [ ] Coyote time, jump buffer et correction de coin fonctionnent (décris comment
      les tester).
- [ ] Mort → respawn quasi instantané ; game over → relance en une touche.
- [ ] Chaque niveau introduit un élément nouveau ; le premier niveau apprend les
      contrôles sans écran de texte long.
- [ ] Records et réglages persistent après rechargement de la page.
- [ ] Les pixels restent nets à toutes les tailles de fenêtre (agrandissement entier).
- [ ] Pause, remapping, « réduire les effets », volumes séparés : présents et fonctionnels.
- [ ] Aucun des éléments interdits dans « Garde-fous ».

## Livrables attendus

1. Le code complet du jeu (`index.html`, `style.css`, `src/` ou `game.js`).
2. `README.md` du jeu : comment le lancer, contrôles, options, comment ouvrir le
   panneau de debug.
3. `GAME_DESIGN.md` : le mini document de design de l'étape 1, à jour.
4. Un court rapport final : ce qui est fait, ce qui reste à améliorer, et le
   résultat de chaque point de la checklist (coché ou non, avec explication).
