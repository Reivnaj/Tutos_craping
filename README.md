# Faisons pas à pas notre premier scrappeur
Un tutorial en markdown qui explique pas à pas la création d'un web scrappeur, en utilisant nokogiri et xpath.

## 1. Nokogiri
Aujourd’hui, pour faire notre premier scrappeur, THP nous demande d’installer Nokogiri. Nokogiri c’est quoi au juste ? C’est une gem (comme twitter !) dont on peut retrouver toute la doc [ici](https://github.com/sparklemotion/nokogiri).
En fait, ça permet d’effectuer des recherches dans des pages webs grâce à des “sélecteurs XPath et CSS3”.

(Le language Ruby a été développé par Yukihiro Matsumoto, et en japonais nokogiri c'est à indiquer un type de scie, donc quelquechose qui scie un page web dans ses parties plus petites.)


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


## 3. CSS

Nokogiri ne travaille seulement avec les Xpaths. 
Elle peut travailler aussi avec le CSS de notre HTML.
Voilà comment.

```
document.css('a_css_class')
```
Tu peux aussi récuperer des info précises dans le html.
Par. ex., si on est en train de extrahir un lien (<a></a>), peut etre on a besoin de le `href` qui se trouve dans le outer html du lien (<a href="my_url"></a>).
Petit reminder, comment distinguer l'outer html du inner html? C'est qu'on trouve dedans la balise c'est inner, c'est qu'on trouve sur le deux cotés de la balise est outer.
Du coup `<tag OUTER HTML>INNER HTML</tag>`.
Comment extrahir donc le href de notre lien?

```
<div>
<a class="my_link_class" href="my_url">LINK</a>
</div>

# méthode1
document.css('.my_link_class') --> retourne "LINK"

# méthode2
document.css('a.my_link_class') --> retourne "LINK"

# méthode3 (enchainer plus méthode .css ensemble) 
document.css('div').css('a') --> retourne "LINK"

# méthode4 (plus compacte) 
document.css('div 'a') --> retourne "LINK"

# méthode5 (extrahir le href)
document.css('a.my_link_class').attribute('href).value --> retourne "my_url"

# méthode6 (extrahir le href)
document.css('a.my_link_class')['href'] --> retourne "my_url"
```

## 4. The Bastards Book of Ruby
Comme beaucoup de choses dans la vie et dans le codage, il n'y a pas _une_ seule façon de faire un web scrappeur.
Le Bouqin des Salauds de Ruby (ou The Bastards Book of Ruby, pour les apolides) offre une petite introduction au web scrapping, avec d'autres Ruby gems.
Ici tu peut trouver le [tutoriel complet](http://ruby.bastardsbook.com/chapters/web-scraping/), ci-dessous on va juste t'expliquer les points clé.

*gem Crack*

Crack c'est une petite gem.
ça ce n'est pas une euphemisme, c'est juste à dire que elle fait peu de chose et a pas beaucoup de funtions.
Mais son point fort c'est qu'elle peut parser du JSON. Pas mal.

Crack va ouvrir ta page web:

```
require 'crack'

url='http://en.wikipedia.org'
document = Crack::JSON.parse(open(url))
```

Et après, elle va parser le contenu selon la tag (le nom de la balise) que tu choisis.
Crack est capable de parser des contenus en XML et en JSON. 
Comment puis-je savoit si mes données sont en XML ou en JSON?
On regarde le type de balise. Una balise entre <> c'est du XML, entre "" c'est du JSON.

```
# Parser du XML
Crack::XML.parse("<my_tag>My_content</my_tag>")  # => {'tag' => 'This is the contents'}
# Parser du JSON
Crack::JSON.parse('{"my_tag":"My_content"}')  # => {'tag' => 'This is the contents'}
```
Crack va donc retourner un hash.
Après, il y a des fonctionalités comme `to_html`,`to_s` etc.
Consultez la [doc complète](https://rubydoc.info/gems/crack) pour une liste exhaustive.

*gem Mechanize*

Nokogiri c'est le parseur de default de la gem Mechanize.
Mechanize nous aide à interagir avec le browser.

Un example pour mieux comprendre les fonctionalités de cette gem.

```
require 'mechanize'

agent = Mechanize.new
page = agent.get('http://google.com/') 

page.links.each do |link| # to check all the links in a page
  puts link.text
end

news = page.links.find { |link| link.text == 'News' }.click
#!! This can be shorten in 
# news = page.link_with(:text => 'News').click 
# or lengthen with
# news = page.link_with(:text => 'News', :href => '/something')
```

On trouve beaucoup d'autres examples dans la [doc complète](http://docs.seattlerb.org/mechanize/GUIDE_rdoc.html)

## 5. Examples concrets
