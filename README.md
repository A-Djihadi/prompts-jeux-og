# Prompts Jeux OG 🎮

Tu viens de l'épisode **« OG Games »** ? Bienvenue.

Ici, pas de jeux tout faits : tu récupères **2 prompts** à donner à ton agent IA
(Claude, ChatGPT, Codex, Cursor… celui que tu utilises) pour qu'il **code un jeu
complet à ta place**. Et pas un jeu fade : les prompts lui demandent d'appliquer
des principes de game design reconnus pour que ton jeu donne envie d'y rejouer.

## Les 2 prompts

| Prompt | Tu obtiens | Style |
| --- | --- | --- |
| [`prompts/jeu-retro-sprites.md`](prompts/jeu-retro-sprites.md) | Un jeu **rétro** (plateforme, shoot'em up ou arcade) | Pixel art, sprites générés par le code, sons 8-bit |
| [`prompts/jeu-action-aventure-flash.md`](prompts/jeu-action-aventure-flash.md) | Un jeu **d'action-aventure** façon jeux Flash | Vectoriel, **zéro sprite**, formes et couleurs dessinées par le code |

Dans les deux cas : **HTML5 Canvas + JavaScript pur**, pas de framework, rien à
installer côté jeu. Ça tourne dans ton navigateur.

## Comment t'en servir (3 étapes)

1. **Ouvre le prompt** qui te tente et copie **tout** ce qui est sous la ligne `---`.
2. **Personnalise (optionnel)** : remplace les trucs entre crochets `[...]`
   (nom du jeu, thème, genre…). Si tu touches à rien, l'agent choisit pour toi.
3. **Colle-le dans ton agent IA** et laisse-le bosser. Il va d'abord écrire un
   petit doc de design, puis coder le jeu étape par étape, puis vérifier une
   checklist qualité.

Pour lancer le jeu ensuite, l'agent te donnera la commande. En général, dans le
dossier du jeu :

```bash
npx serve .
# ou
python -m http.server
```

…puis tu ouvres l'adresse affichée dans ton navigateur.

## Astuces

- **Un agent qui peut créer des fichiers et lancer des commandes** (agent de code
  dans un éditeur ou un terminal) marche beaucoup mieux qu'un simple chat.
- **Appuie sur `Tab` en jeu** : les prompts demandent un panneau qui active/coupe
  chaque effet (tremblement d'écran, particules, flash…). Coupe tout, puis remets
  tout : tu vas sentir la différence. C'est ça, le « game feel ».
- **Le jeu te semble trop dur ou trop mou ?** Demande à ton agent de modifier les
  valeurs dans l'objet `CONFIG` : tout le réglage est centralisé là.
- **Itère** : « ajoute un boss », « rends le dash plus nerveux », « change la
  palette »… ton agent connaît déjà la structure du jeu.

## Pourquoi ces prompts marchent

Chaque mécanique demandée est taguée `[P1]`, `[P2]`… et renvoie à un principe de
game design **sourcé** (talks GDC, articles de devs, publications scientifiques) :
game feel, « juice », contrôles qui pardonnent les erreurs, échec rapide,
difficulté progressive, récompenses variables, etc.

👉 Tout est expliqué avec les liens dans [`references/game-design.md`](references/game-design.md).

Et c'est « addictif » dans le bon sens : **pas** d'argent réel, pas de loot box,
pas de récompense quotidienne qui te force à revenir. Juste un jeu bien fait.

## Licence

Voir [`LICENSE`](LICENSE) (en cours de validation).
