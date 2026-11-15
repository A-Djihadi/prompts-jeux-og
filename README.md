# Prompts jeux OG

Tu viens de l'épisode « OG Games » ? Bienvenue.

Ici, pas de jeux tout faits. Tu récupères 2 prompts à donner à ton agent IA
(Claude, ChatGPT, Codex, Cursor ou celui que tu utilises). Il code un jeu
complet à ta place. Et pas un jeu fade : les prompts lui demandent d'appliquer
des principes de game design reconnus. Résultat, ton jeu donne envie d'y rejouer.

## Récupère le repo

```bash
git clone https://github.com/A-Djihadi/prompts-jeux-og.git
```

## Les 2 prompts

| Prompt | Tu obtiens | Style |
| --- | --- | --- |
| [`prompts/jeu-retro-sprites.md`](prompts/jeu-retro-sprites.md) | Un jeu rétro (plateforme, shoot'em up ou arcade) | Pixel art, sprites générés par le code, sons 8-bit |
| [`prompts/jeu-action-aventure-flash.md`](prompts/jeu-action-aventure-flash.md) | Un jeu d'action-aventure façon jeux Flash | Vectoriel, zéro sprite, formes et couleurs dessinées par le code |

Dans les deux cas, c'est du HTML5 Canvas et du JavaScript pur. Pas de framework,
rien à installer côté jeu. Ça tourne dans ton navigateur.

## Comment t'en servir en 3 étapes

1. Ouvre le prompt qui te tente. Copie tout ce qui est sous la ligne `---`.
2. Personnalise si tu veux. Remplace ce qui est entre crochets `[...]` : nom du
   jeu, thème, genre. Si tu ne touches à rien, l'agent choisit pour toi.
3. Colle-le dans ton agent IA et laisse-le bosser. Il écrit d'abord un petit doc
   de design. Ensuite il code le jeu étape par étape. À la fin, il vérifie une
   checklist qualité.

Pour lancer le jeu, l'agent te donnera la commande. En général, dans le dossier
du jeu :

```bash
npx serve .
# ou
python -m http.server
```

Ouvre ensuite l'adresse affichée dans ton navigateur.

## Astuces

- Prends un agent qui peut créer des fichiers et lancer des commandes. Un agent
  de code dans un éditeur ou un terminal marche bien mieux qu'un simple chat.
- Appuie sur `Tab` en jeu. Les prompts demandent un panneau qui active ou coupe
  chaque effet : tremblement d'écran, particules, flash. Coupe tout, puis remets
  tout. Tu vas sentir la différence. C'est ça, le « game feel ».
- Le jeu te semble trop dur ou trop mou ? Demande à ton agent de modifier les
  valeurs dans l'objet `CONFIG`. Tout le réglage est centralisé là.
- Itère. Demande « ajoute un boss », « rends le dash plus nerveux » ou « change
  la palette ». Ton agent connaît déjà la structure du jeu.

## Pourquoi ces prompts marchent

Chaque mécanique demandée porte un tag comme `[P1]` ou `[P2]`. Il renvoie à un
principe de game design sourcé : talks GDC, articles de devs, publications
scientifiques. On parle de game feel, de « juice », de contrôles qui pardonnent
les erreurs, d'échec rapide, de difficulté progressive et de récompenses variables.

Tout est expliqué avec les liens dans [`references/game-design.md`](references/game-design.md).

Et c'est « addictif » dans le bon sens. Pas d'argent réel, pas de loot box, pas
de récompense quotidienne qui te force à revenir. Juste un jeu bien fait.

## Licence

Licence : CC-BY-4.0 (attribution Djihadi Ahamdy). Le texte complet est dans
[`LICENSE`](LICENSE).
