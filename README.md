# Faisons pas à pas notre premier scrappeur
Un tutorial en markdown qui explique pas à pas la création d'un web scrappeur, en utilisant nokogiri et xpath.

## 1. Nokogiri
Aujourd’hui, pour faire notre premier scrappeur, THP nous demande d’installer Nokogiri. Nokogiri c’est quoi au juste ? C’est une gem (comme twitter !) dont on peut retrouver toute la doc [ici](https://github.com/sparklemotion/nokogiri).
En fait, ça permet d’effectuer des recherches dans des pages webs grâce à des “sélecteurs XPath et CSS3”.
Pour la petite histoire, le mot "nokogiri" est un mot japonais qui signifie une scie : en fait, en utilisant la gem Nokogiri, on "scie" notre page web en petits bouts. 

### 1.1. Installation de Nokogiri
Avant toute chose, il faut installer Nokogiri.
On crée un dossier de travail “PremierScrappeur” avec un dossier lib, un dossier spec, … et un fichier Gemfile.
Dans le fichier Gemfile, on met les lignes suivantes :
```
source "https://rubygems.org"
ruby '2.5.1'
gem 'rubocop', '~> 0.57.2'
gem 'rspec'
gem 'pry'
```
Et on ajoute une ligne pour Nokogiri :
```
gem 'nokogiri'
```
On enregistre ce fichier et on lance sur notre terminal la commande :
```
bundle install
```
### 2.2. Comment l’utiliser ?
On crée un fichier scrappeur.rb et on lui indique qu’on va utiliser Nokogiri :
```
require 'nokogiri'
```
Pour pouvoir faire des recherches sur une page web, il faut ajouter un autre require :
```
require 'open-uri'
```
Tout est prêt : on va aller récupérer notre première page web !
```
page = Nokogiri::HTML(open("https://fr.wikipedia.org/wiki/Web_scraping"))
```
Notre variable `page` contient la page web https://fr.wikipedia.org/wiki/Web_scraping.
Si tu tentes un `puts page` tu verras apparaître sur ton terminal le code html de wikipedia !
Maintenant qu'on sait récupérer une page web, on va apprendre à faire des récupérer des données sur cette page.
C'est là qu'XPath entre en jeu

## 2. Xpath
Toutes les infos de la page web ne nous intéressent pas.
XPath permet de ne récupérer que les informations que l'on veut. C'est un "sélectionneur".
Par exemple, sur notre page wikipedia, on ne voudrait extraire que le titre *Web scraping*.
Voici un extrait du code html de cette page :
```
<html>
  <head>
    ...
  </head>
  <body>
    ...
    <h1 id="firstHeading" class="firstHeading" lang="fr"><i><span lang="en">Web scraping</span></i></h1>
  </body>
</html>
```
On voit que le titre est dans une balise `<h1>`.
On ajoute une ligne dans le fichier scrappeur.rb :
```
puts page.xpath('//h1')
```
On verra apparaître sur le terminal :
```
<h1 id="firstHeading" class="firstHeading" lang="fr"><i><span lang="en">Web scraping</span></i></h1>
```
En fait, on vient d'extraire toutes les balises `h1` de la page stockée dans la variable `page`.
Si on veut ne récupérer que le texte (sans le code html), il faut modifier la ligne :
```
puts page.xpath('//h1').text
```
On obtient maintenant *Web scraping*

On peut faire ça avec tous les éléments de la page, du moment qu'on insère la bonne recherche dans le XPath.
Voici une super [Cheatsheet](https://devhints.io/xpath), dont voici un petit extrait (*à tester soi-même pour voir le résultat*):

| Balise à récupérer        | Code Xpath correspondant  |
| ------------------------- | ------------------------- |
| Tous les `<li>` dans un `<ul>` | `page.xpath('//ul/li')` |
| Tous les `<a>` dans un `<li>` dans un `<ul>` | `page.xpath('//ul/li/a')` |
| Tous les `<p>` dans un `<div>` (mais il peut y avoir des étapes intermédiaires) | `page.xpath('//div//p')` |
| Tous les `<h1>` qui ont un `id=firstHeading` | `page.xpath('//h1[@id="firstHeading"]')` |
| Tous les `<h1>` qui ont un `class=firstHeading` | `page.xpath('//h1[@class="firstHeading"]')` |
| Tous les `href`(lien) dans un `<a>` | `page.xpath('//a/@href')` |


Il faut séparer les étapes par des /. On en utilise 2 (//) si on veut sélectionner un descendant qui n'est pas direct.

Pour plus d'infos, on peut aller jeter un oeil au [tutorial W3School sur le Xpath](https://www.w3schools.com/xml/xpath_intro.asp).


## 3. Bastards Book
## 4. Examples concrets
## 5. Pour aller plus loin
