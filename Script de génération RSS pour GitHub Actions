# rss_generator.py

import requests
from bs4 import BeautifulSoup
import datetime
import pytz
import xml.etree.ElementTree as ET
from xml.dom import minidom
import os

def get_tweets(username):
    """Récupère les tweets récents d'un utilisateur X (Twitter)"""
    url = f"https://nitter.net/{username}"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }
    
    try:
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        
        soup = BeautifulSoup(response.text, 'html.parser')
        tweets = []
        
        tweet_elements = soup.select('.timeline-item')
        
        for tweet in tweet_elements:
            try:
                content = tweet.select_one('.tweet-content')
                if not content:
                    continue
                    
                # Récupérer le texte du tweet
                tweet_text = content.get_text(strip=True)
                
                # Récupérer l'URL du tweet
                permalink = tweet.select_one('.tweet-link')
                tweet_url = f"https://x.com{permalink['href']}" if permalink else None
                
                # Récupérer la date du tweet
                date_element = tweet.select_one('.tweet-date a')
                tweet_date = date_element.get('title') if date_element else None
                
                # Convertir la date en objet datetime
                if tweet_date:
                    date_obj = datetime.datetime.strptime(tweet_date, '%b %d, %Y · %I:%M %p %Z')
                else:
                    date_obj = datetime.datetime.now(pytz.utc)
                
                # Récupérer les images du tweet s'il y en a
                images = []
                media_elements = tweet.select('.attachment .still-image')
                for media in media_elements:
                    img = media.select_one('img')
                    if img and 'src' in img.attrs:
                        # Convertir les URLs de Nitter vers des URLs Twitter
                        img_url = img['src']
                        if img_url.startswith('/'):
                            img_url = f"https://nitter.net{img_url}"
                        images.append(img_url)
                
                tweets.append({
                    'text': tweet_text,
                    'url': tweet_url,
                    'date': date_obj,
                    'images': images
                })
                
            except Exception as e:
                print(f"Erreur lors de l'analyse d'un tweet: {e}")
                continue
                
        return tweets
        
    except Exception as e:
        print(f"Erreur lors de la récupération des tweets: {e}")
        return []

def create_rss(username, tweets, output_file="pokebeach_rss.xml"):
    """Crée un fichier RSS à partir des tweets récupérés"""
    rss = ET.Element('rss', version='2.0')
    channel = ET.SubElement(rss, 'channel')
    
    # Informations du canal
    title = ET.SubElement(channel, 'title')
    title.text = f"{username} - Tweets"
    
    link = ET.SubElement(channel, 'link')
    link.text = f"https://x.com/{username}"
    
    description = ET.SubElement(channel, 'description')
    description.text = f"Flux RSS des tweets de {username}"
    
    language = ET.SubElement(channel, 'language')
    language.text = 'fr-fr'
    
    lastBuildDate = ET.SubElement(channel, 'lastBuildDate')
    lastBuildDate.text = datetime.datetime.now().strftime('%a, %d %b %Y %H:%M:%S +0000')
    
    # Ajouter les tweets comme éléments
    for tweet in tweets:
        item = ET.SubElement(channel, 'item')
        
        item_title = ET.SubElement(item, 'title')
        # Utiliser les 50 premiers caractères du tweet comme titre
        title_text = tweet['text'][:50] + ('...' if len(tweet['text']) > 50 else '')
        item_title.text = title_text
        
        item_link = ET.SubElement(item, 'link')
        item_link.text = tweet['url']
        
        item_guid = ET.SubElement(item, 'guid')
        item_guid.text = tweet['url']
        
        item_pubDate = ET.SubElement(item, 'pubDate')
        item_pubDate.text = tweet['date'].strftime('%a, %d %b %Y %H:%M:%S +0000')
        
        item_description = ET.SubElement(item, 'description')
        
        # Préparation de la description HTML avec le texte et les images
        description_html = f"<p>{tweet['text']}</p>"
        
        if tweet['images']:
            description_html += "<div style='margin-top: 10px;'>"
            for img_url in tweet['images']:
                description_html += f"<img src='{img_url}' style='max-width: 100%; margin-bottom: 10px;' /><br/>"
            description_html += "</div>"
            
        item_description.text = description_html
        
    # Créer un XML formaté
    rough_string = ET.tostring(rss, 'utf-8')
    reparsed = minidom.parseString(rough_string)
    
    # Écrire dans un fichier
    with open(output_file, 'w', encoding='utf-8') as f:
        f.write(reparsed.toprettyxml(indent="  "))
        
    print(f"Flux RSS créé avec succès dans le fichier {output_file}")
    return output_file

def main():
    username = "pokebeach"
    
    print(f"Récupération des tweets de {username}...")
    tweets = get_tweets(username)
    
    if not tweets:
        print("Aucun tweet récupéré. Vérifiez le nom d'utilisateur ou votre connexion internet.")
        return
        
    print(f"Nombre de tweets récupérés: {len(tweets)}")
    
    # Génération du flux RSS principal
    output_file = "pokebeach_rss.xml"
    create_rss(username, tweets, output_file)
    
    # Génération d'une page HTML simple avec un lien vers le flux RSS
    with open("index.html", "w", encoding="utf-8") as f:
        f.write(f"""<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flux RSS pour @{username}</title>
    <style>
        body {{ font-family: Arial, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; }}
        h1 {{ color: #1DA1F2; }}
        .container {{ background: #f8f9fa; border-radius: 10px; padding: 20px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }}
        .info {{ margin-top: 20px; color: #666; }}
        .rss-link {{ display: inline-block; background: #FF9800; color: white; padding: 10px 15px;
                    text-decoration: none; border-radius: 5px; margin-top: 15px; }}
        .refresh-info {{ margin-top: 30px; font-size: 0.9em; color: #888; }}
    </style>
</head>
<body>
    <div class="container">
        <h1>Flux RSS pour @{username}</h1>
        <p>Ce service génère automatiquement un flux RSS à partir du compte Twitter/X de @{username}.</p>
        
        <a href="{output_file}" class="rss-link">S'abonner au flux RSS</a>
        
        <div class="info">
            <p>Pour utiliser ce flux RSS:</p>
            <ol>
                <li>Copiez l'URL: <code>https://votre-nom.github.io/pokebeach-rss/{output_file}</code></li>
                <li>Collez cette URL dans votre lecteur RSS préféré</li>
            </ol>
        </div>
        
        <div class="refresh-info">
            <p>Le flux est automatiquement mis à jour toutes les heures.</p>
            <p>Dernière mise à jour: {datetime.datetime.now().strftime('%d/%m/%Y à %H:%M')}</p>
        </div>
    </div>
</body>
</html>""")
    
    print("Page d'accueil générée avec succès")

if __name__ == "__main__":
    main()
