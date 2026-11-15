# Principes de game design retenus (et sources)

Ce fichier explique **pourquoi** les deux prompts demandent ce qu'ils demandent.
Chaque principe ci-dessous est sourcé. On a gardé uniquement ce qui est documenté
par son auteur ou par une publication ; tout ce qu'on n'a pas pu vérifier est listé
en bas dans « Ce qu'on n'a pas retenu ».

> **Note sur le mot « addictif ».** Ici, « addictif » veut dire « donne envie de
> relancer une partie » : bon ressenti des contrôles, échec pas frustrant,
> progression visible, récompenses qui tombent pendant le jeu. Les mécaniques qui
> exploitent le joueur (argent réel, rendez-vous forcés, grind) sont **exclues**
> (voir principe 12).

Les tags `[P1]`…`[P14]` sont les mêmes que dans les prompts.

---

## [P1] Game feel : contrôle en temps réel + réponse immédiate

- **Source :** Steve Swink, *Game Feel: A Game Designer's Guide to Virtual Sensation*,
  Morgan Kaufmann / CRC Press, 2008. Chapitre 1 :
  <https://mycours.es/gamedesign2014/files/2014/10/Game-Feel-Steve-Swink-chapter-1.pdf>
- **Ce que dit la source :** le game feel est « le contrôle en temps réel d'objets
  virtuels dans un espace simulé, avec des interactions mises en valeur par du
  polish ». Le *polish* désigne tout effet qui renforce l'interaction **sans changer
  la simulation** (poussière aux pieds, son d'impact, camera shake, squash & stretch).
- **Pourquoi on le garde :** ça donne une règle d'architecture claire : la logique
  du jeu (simulation) et les effets (polish) sont deux couches séparées. On peut
  couper tous les effets et le jeu reste jouable à l'identique.
- **Dans les prompts :** séparation simulation / rendu-effets ; chaque effet est
  derrière un interrupteur dans un objet `CONFIG`.

## [P2] « Juice » : beaucoup de réponse pour peu d'input

- **Source :** Martin Jonasson & Petri Purho, *Juice It or Lose It*, Nordic Game Indie
  Night 2012, repris à GDC Europe 2012.
  - GDC Vault : <https://www.gdcvault.com/play/1016487/juice-it-or-lose>
  - Code de la démo : <https://github.com/grapefrukt/juicy-breakout>
  - Résumé détaillé : <https://gdad.wiki/wiki/sources/source-juice-it-or-lose-it>
- **Ce que dit la source :** les auteurs prennent un casse-briques basique et, sans
  changer ses règles, ajoutent couche par couche : couleurs, tweening/easing,
  squash & stretch, son, particules, screen shake, flash blanc à l'impact. Résumé :
  « maximum output for minimum input ». Purho décrit le son comme l'ajout le plus
  rentable. Dans leur code, chaque effet est un booléen indépendant activable.
- **Limite connue :** Folmer Kelly (GDC Europe 2014) a défendu que trop de juice peut
  nuire à l'immersion :
  <https://www.gamedeveloper.com/design/video-indies-resist-the-urge-to-juice-it-or-lose-it->.
  D'où la demande de rendre chaque effet réglable/désactivable.
- **Dans les prompts :** liste d'effets à implémenter (flash d'impact, tweening,
  squash & stretch, particules, sons synthétisés), tous en interrupteurs.

## [P3] Les « petits trucs » des jeux d'action (screen shake, hit stop)

- **Source :** Jan Willem Nijman (Vlambeer), *The Art of Screenshake*, INDIGO Classes 2013.
  - Vidéo : <https://www.youtube.com/watch?v=SkgkIXZ_13Y>
  - Article : <https://www.gamedeveloper.com/design/vlambeer-co-founder-shares-advice-on-building-better-action-games>
- **Ce que dit la source :** il transforme un shooter plat en jeu agréable avec une
  série de petites modifications : animations et sons de base, projectiles plus gros
  et plus rapides, muzzle flash, recul, screen shake, pause du jeu de quelques
  millisecondes à l'impact (hit stop), travail sur la caméra.
- **Dans les prompts :** screen shake, hit stop, projectiles lisibles, muzzle flash,
  recul. Surtout dans le prompt action-aventure (combat).

## [P4] Pardonner les erreurs de timing (input forgiveness)

- **Source :** Maddy Thorson, *Celeste & Forgiveness* :
  <https://www.mattmakesgames.com/articles/celeste_and_forgiveness/index.html>
- **Ce que dit la source :** Celeste élargit discrètement les fenêtres de timing en
  faveur du joueur : *coyote time* (on peut encore sauter juste après avoir quitté
  un rebord), *jump buffering* (un saut pressé juste avant d'atterrir part à
  l'atterrissage), gravité réduite au sommet du saut si on tient le bouton,
  correction de coin quand on se cogne la tête. Conclusion de l'autrice : c'est une
  grande raison pour laquelle le jeu « paraît bienveillant même s'il est très
  difficile ». Elle précise ne pas avoir inventé ces techniques. L'article indique
  aussi que Celeste tourne en 320x180.
- **Dans les prompts :** coyote time, jump buffer, correction de coin (si jeu de
  plateforme) ; buffer d'attaque/esquive (si jeu d'action). Les durées sont des
  constantes dans `CONFIG`, à régler en jouant.

## [P5] Échec rapide, retour immédiat, niveaux courts

- **Source :** Edmund McMillen & Tommy Refenes, *Postmortem: Team Meat's Super Meat Boy* :
  <https://www.gamedeveloper.com/audio/postmortem-team-meat-s-i-super-meat-boy-i->
  et *Super Meat Boy's McMillen Explains 'Why So Hard?'* :
  <https://www.gamedeveloper.com/game-platforms/-i-super-meat-boy-i-s-mcmillen-explains-why-so-hard->
- **Ce que dit la source :** pour garder la difficulté sans la frustration :
  « supprimer les vies, réduire le temps de respawn, garder les niveaux courts et
  l'objectif toujours en vue », plus du feedback positif constant. Le respawn est
  quasi instantané, les transitions sont accélérées. À la fin d'un niveau, le replay
  de toutes les tentatives rappelle au joueur qu'il a progressé.
- **Dans les prompts :** relance en une touche, respawn immédiat, niveaux/salles
  courts, objectif visible, récap de fin de run qui montre la progression.

## [P6] Flow : défi adapté au niveau, objectifs clairs, feedback

- **Sources :**
  - Jenova Chen, *Flow in Games (and Everything Else)*, Communications of the ACM 50(4), 2007 :
    <https://cacm.acm.org/opinion/flow-in-games-and-everything-else/>
  - Penelope Sweetser & Peta Wyeth, *GameFlow: a model for evaluating player enjoyment
    in games*, ACM Computers in Entertainment 3(3), 2005 :
    <https://doi.org/10.1145/1077246.1077253>
- **Ce que disent les sources :** Chen recommande de garder le joueur dans sa
  « Flow Zone » et d'offrir des choix adaptatifs intégrés à l'activité principale,
  pour que le flow ne soit pas interrompu. GameFlow identifie 8 éléments du plaisir
  de jeu : concentration, challenge, compétences du joueur, contrôle, objectifs
  clairs, feedback, immersion, interaction sociale ; le défi doit correspondre au
  niveau du joueur, avec objectifs clairs et feedback sur la progression.
- **Dans les prompts :** courbe de difficulté progressive, objectif courant toujours
  affiché, feedback visuel/sonore sur chaque progrès, tutoriel intégré au premier
  niveau plutôt qu'un écran de texte.

## [P7] Difficulté dynamique, mais invisible et non punitive

- **Sources :**
  - Robin Hunicke, *The case for dynamic difficulty adjustment in games*, ACE '05, 2005 :
    <https://doi.org/10.1145/1178477.1178573>
  - Game Accessibility Guidelines, « Allow difficulty level to be altered during
    gameplay, either through settings or adaptive difficulty » :
    <https://gameaccessibilityguidelines.com/full-list/>
- **Ce que disent les sources :** Hunicke note que, selon l'idée reçue, les joueurs
  se sentent « trichés » si le jeu s'ajuste ; pour être efficace, l'ajustement doit
  se faire **sans perturber ni dégrader** l'expérience de base. Elle présente un
  système (Hamlet) dont les premiers résultats remettent en cause ces idées reçues.
- **Dans les prompts :** ajustement léger (ex. un peu plus de soins/drops après
  plusieurs échecs d'affilée), jamais de changement visible des règles, toujours
  désactivable, plus un choix de difficulté manuel.

## [P8] Récompenses à ratio variable (dans le jeu uniquement)

- **Source :** John Hopson, *Behavioral Game Design*, Gamasutra, 2001 :
  <https://www.gamedeveloper.com/design/behavioral-game-design>
  (et son retour 10 ans après : <https://www.gamedeveloper.com/design/10-years-of-behavioral-game-design-with-bungie-s-research-boss>)
- **Ce que dit la source :** un ratio **fixe** (ex. une vie tous les 20 ennemis)
  produit une pause après chaque récompense puis une rafale d'activité ; un ratio
  **variable** (le nombre exact change à chaque fois) donne l'activité la plus
  élevée et la plus régulière des schémas étudiés, sans cette pause. Hopson note
  aussi qu'une baisse soudaine des récompenses est très mal vécue et qu'il faut la
  faire graduellement.
- **Dans les prompts :** drops aléatoires à la mort des ennemis (bonus, pièces,
  power-ups), combinés à quelques paliers fixes (fin de niveau, boss). Les
  récompenses ne baissent jamais brutalement d'un niveau à l'autre. **Aucune**
  monnaie réelle, aucune loot box payante (voir P12).

## [P9] Compétence et autonomie

- **Source :** Richard M. Ryan, C. Scott Rigby & Andrew Przybylski, *The Motivational
  Pull of Video Games: A Self-Determination Theory Approach*, Motivation and Emotion
  30, 2006 : <https://doi.org/10.1007/s11031-006-9051-8>
- **Ce que dit la source :** sur 4 études, l'autonomie et la compétence perçues en
  jeu sont associées au plaisir et à l'envie de rejouer ; la compétence et
  l'autonomie perçues sont liées au caractère intuitif des contrôles.
- **Dans les prompts :** contrôles simples et intuitifs, vrais choix (ex. choisir 1
  amélioration parmi plusieurs entre deux niveaux), affichage clair des progrès du
  joueur (score, record, stats de fin de run).

## [P10] Le fun vient de l'apprentissage : introduire des nouveautés régulièrement

- **Source :** Raph Koster, *A Theory of Fun for Game Design* (2004, 2e éd. 2013).
  Présentation par Wired : <https://www.wired.com/2013/11/theory-of-fun-revised/>
- **Ce que dit la source :** « Fun is just another word for learning » ; une fois
  un motif maîtrisé, simplement le répéter n'est plus forcément amusant, c'est
  pousser ses limites qui l'est.
- **Dans les prompts :** chaque niveau/zone introduit **un** élément nouveau
  (ennemi, obstacle, capacité), d'abord dans une situation sans danger, puis combiné
  avec les éléments connus.

## [P11] Partir de l'expérience visée (MDA)

- **Source :** Robin Hunicke, Marc LeBlanc & Robert Zubek, *MDA: A Formal Approach to
  Game Design and Game Research*, AAAI Workshop on Challenges in Game AI, 2004 :
  <https://users.cs.northwestern.edu/~hunicke/MDA.pdf>
- **Ce que dit la source :** un jeu se décompose en Mécaniques (règles, données,
  algorithmes), Dynamiques (comportement à l'exécution) et Esthétiques (émotions
  visées chez le joueur). Le designer part des mécaniques, le joueur ressent d'abord
  les esthétiques.
- **Dans les prompts :** l'agent doit d'abord écrire un mini document de design qui
  part des émotions visées puis liste les mécaniques qui les servent.

## [P12] Garde-fou éthique : pas de dark patterns

- **Source :** José P. Zagal, Staffan Björk & Chris Lewis, *Dark Patterns in the Design
  of Games*, Foundations of Digital Games 2013 :
  <https://my.eng.utah.edu/~zagal/Papers/Zagal_et_al_DarkPatterns.pdf>
- **Ce que dit la source :** certains éléments de design servent le créateur contre
  l'intérêt du joueur. Catégories : temporels (*grinding*, *playing by appointment*),
  monétaires (*pay to skip*, contenu pré-livré, rivalités monétisées), capital social
  (pyramides sociales, usurpation d'identité).
- **Dans les prompts :** interdits explicites : argent réel, timers qui obligent à
  revenir à heure fixe, grind répétitif pour progresser, faux messages sociaux.
  On veut que le joueur revienne parce que c'est bon, pas parce qu'il est piégé.

## [P13] Accessibilité de base

- **Source :** Game Accessibility Guidelines (liste complète) :
  <https://gameaccessibilityguidelines.com/full-list/>
- **Ce que dit la source (niveau « basic » et « intermediate ») :** permettre de
  remapper les contrôles, garder les contrôles simples, éviter les images qui
  clignotent, pouvoir couper les mouvements d'arrière-plan, volumes séparés
  musique/effets, tutoriels interactifs, pouvoir démarrer sans traverser plusieurs
  menus, rappeler les contrôles et l'objectif en jeu, sauvegarder les réglages.
- **Dans les prompts :** pause à tout moment, touches remappables, option « réduire
  les effets » (shake/flash), volumes séparés, lancement en une touche, réglages
  sauvegardés en `localStorage`.

## [P14] Télégraphier les attaques ennemies

- **Source :** Mike Stout, *Enemy Attacks and Telegraphing*, Gamasutra, 2015 :
  <https://www.gamedeveloper.com/design/enemy-attacks-and-telegraphing>
- **Ce que dit la source :** dans un système où le joueur doit esquiver, il doit
  d'abord savoir que les dégâts arrivent. Avant l'attaque, un court délai et un
  signal (animation d'élan, effet visuel de charge, son) annoncent le coup ; si le
  joueur est touché ensuite, c'est juste, parce qu'il savait ce qu'on attendait de lui.
- **Dans les prompts :** chaque attaque ennemie est annoncée par un signal visuel
  et/ou sonore avant de frapper (prompt action-aventure surtout).

---

## Ce qu'on n'a pas retenu (et pourquoi)

- **Valeurs chiffrées « magiques »** (durée exacte de coyote time, de hit stop, de
  screen shake, longueur idéale d'une session…) : les sources montrent des
  techniques, pas des valeurs universelles. Les prompts demandent des constantes
  réglables dans `CONFIG`, pas des chiffres imposés.
- **Récompenses quotidiennes / streaks / notifications** : relevées comme patterns
  temporels douteux (P12, *playing by appointment*). Exclues.
- **Loot boxes / monnaie premium** : patterns monétaires (P12). Exclus.
- **« Near miss » et pertes déguisées en gains** : techniques issues des machines
  à sous ; on n'a pas trouvé de source de game design qui les recommande pour un
  jeu, et elles relèvent de la manipulation. Exclues.
