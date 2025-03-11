# Flux RSS pour PokeBeach Twitter/X

Ce dépôt génère automatiquement un flux RSS à partir du compte Twitter/X de [@pokebeach](https://x.com/pokebeach).

## Utilisation

Pour vous abonner au flux RSS de PokeBeach:

1. Copiez l'URL suivante:
   ```
   https://VOTRE-NOM-UTILISATEUR.github.io/pokebeach-rss/pokebeach_rss.xml
   ```
   (Remplacez VOTRE-NOM-UTILISATEUR par votre nom d'utilisateur GitHub)

2. Collez cette URL dans votre lecteur RSS préféré

## Fonctionnement

- Le flux est mis à jour automatiquement toutes les heures grâce à GitHub Actions
- Le script utilise une méthode d'extraction alternative (Nitter) pour récupérer les tweets sans API
- Le flux inclut les textes des tweets, les dates et les images

## Personnalisation

Si vous souhaitez suivre un autre compte:

1. Modifiez la variable `username` dans le fichier `rss_generator.py`
2. Commitez et poussez les changements
3. GitHub Actions générera automatiquement un nouveau flux RSS

## Remarques

Ce service est fourni à titre informatif uniquement et n'est pas affilié à X/Twitter ou PokeBeach.
