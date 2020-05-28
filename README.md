# Faisons pas à pas notre premier scrappeur
Un tutorial en markdown qui explique pas à pas la création d'un web scrappeur, en utilisant nokogiri et xpath.

## 1. Nokogiri
Aujourd’hui, pour faire notre premier scrappeur, THP nous demande d’installer Nokogiri. Nokogiri c’est quoi au juste ? C’est une gem (comme twitter !) dont on peut retrouver toute la doc [ici](https://github.com/sparklemotion/nokogiri).
En fait, ça permet d’effectuer des recherches dans des pages webs grâce à des “sélecteurs XPath et CSS3”.

Le language Ruby a été développé par Yukihiro Matsumoto, et en japonais 'nokogiri' est un type de scie. La gem Nokogiri est donc quelquechose qui scie une page web en petits bouts :)


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
Maintenant qu'on sait récupérer une page web, on va apprendre à récupérer des données sur cette page.
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

Nokogiri ne fonctionne pas seulement avec les Xpaths.
Elle peut aussi utiliser avec le CSS de notre HTML.
Voilà comment :

```
document.css('a_css_class')
```
Tu peux aussi récuperer des info précises dans le html.
Par ex., si on est en train d'extraire un lien (`<a></a>`), peut être qu'on a besoin du `href` qui se trouve dans le 'outer' html du lien (`<a href="my_url"></a>`).
Petit reminder, comment distinguer l'outer html du inner html? Ce qu'on trouve dans la balise est 'inner', ce qu'on trouve sur les deux cotés de la balise est 'outer'.
Du coup `<tag OUTER HTML>INNER HTML</tag>`.
Donc, comment extraire le href de notre lien ?

```
<div>
<a class="my_link_class" href="my_url">LINK</a>
</div>

# méthode1
document.css('.my_link_class') --> retourne "LINK"

# méthode2
document.css('a.my_link_class') --> retourne "LINK"

# méthode3 (extraire le div, puis méthode .css ensemble)
document.css('div').css('a') --> retourne "LINK"

# méthode4 (plus compacte)
document.css('div 'a') --> retourne "LINK"

# méthode5 (extraire le href)
document.css('a.my_link_class').attribute('href).value --> retourne "my_url"

# méthode6 (extraire le href)
document.css('a.my_link_class')['href'] --> retourne "my_url"
```

## 4. The Bastards Book of Ruby
Comme beaucoup de choses dans la vie et dans le code, il n'y a pas _une_ seule façon de faire un web scrappeur.
Le Bouqin des Salauds de Ruby (ou The Bastards Book of Ruby, pour les anglophones) offre une petite introduction au web scrapping, avec d'autres Ruby gems.
Tu peux trouver le [tutoriel complet ici](http://ruby.bastardsbook.com/chapters/web-scraping/), ci-dessous on va juste t'expliquer les points clés.

*gem Crack*

Crack c'est une petite gem.
Ce n'est pas un euphemisme, c'est juste pour dire qu'elle fait peu de chose et n'a pas beaucoup de funtions.
Mais, son point fort c'est qu'elle peut parser du JSON. Pas mal, non ?

Crack va ouvrir ta page web:

```
require 'crack'

url='http://en.wikipedia.org'
document = Crack::JSON.parse(open(url))
```

Et après, elle va parser le contenu selon le tag (le nom de la balise) que tu choisis.
Crack est capable de parser des contenus en XML et en JSON.
Comment puis-je savoir si mes données sont en XML ou en JSON?
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

Nokogiri c'est le parseur par défaut de la gem Mechanize.
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

On trouve beaucoup d'autres exemples dans la [doc complète](http://docs.seattlerb.org/mechanize/GUIDE_rdoc.html)

## 5. Exemples concrets
"Bon ok c'est cool tout ça. Mais concrètement ça donne quoi le Web scraping ?" Tu ne fais pas si bien dire jeune hacker en herbe, on va scrapper du concret !
Plus haut, on a vu de supperbes ingrédients pour notre recette de scrappeur aux petits oignons.


Voici comment nous allons procéder :

* Nous allons préparer notre environnement de travail.
* Puis choisir soigneusement les informations que l'on va aller scapper.
* Et pour finir on va coder le bébé.

### 5.1 L'environnement
Pour faire un bon scrappeur tu as besoin d'un dossier carré. Donc commence par créer un dossier `scrappeur` dans lequel tu vas créer un fichier `Gemfile` avec au minimum la `gem 'nokogiri'` et la `gem 'pry'` dedans. La première pour les raisons expliquées plus haut et la seconde pour éviter de relancer notre scrappeur toute les minutes. Ce qui nous donne ce Gemfile :
```ruby
source "https://rubygems.org"

gem 'nokogiri'
gem 'pry'
```
 Puis un dossier `lib` dans lequel tu vas créer un fichier `mon_scrappeur.rb`, c'est là où tu vas écrir le code de ton scrappeur.
Le tout devrait ressembler à ça :
```
scrappeur
        |
        |-Gemfile
        |
        |-lib
            |
            |-mon_scrappeur.rb

```
Super ! On a plus qu'a faire un `bundle install` dans notre dossier `scrappeur`. Ensuite il ne nous reste plus qu'a trouver quoi scrapper et écrire le code. Easy !

### 5.2 Qu'est-ce qu'on scrap ?
Ok maintenant le but est que tu intègre qu'avec un peu de bouteille tu pourra aller chercher des masses d'informations sur des pages web et les trier comme tu veux en un rien de temps. Il existe des tonnes d'exemples qui vont du simple outil amateur pour faire mumuse à l'usage industriel du scraping.
Ici on va juste voir ensemble les bases et après "sky is the limit". Donc, qu'est-ce qu'on scrap ?

On va dire que tu es arrivé à la fin de THP. Tu as fait un super projet final qui a confirmé tes compétences et t'a donné de l'assurance. Maintenant tu te dis que c'est peut être le moment de voir ce que tu vaux sur le marché de l'emploi. Naturellement tu vas faire un tour sur [indeed](https://www.indeed.fr/emplois?q=D%C3%A9veloppeur%20Web%20ruby%20on%20rails&l=paris&vjk=3d896a7bb658f967) par ce que c'est le premier resultat qui tombe sur google.

C'est bien ça, on va faire du scraping sur Indeed !

### 5.3 En ruby ça donne quoi ?
C'est parti on passe au code, tu vas voir c'est facile.
Les premières lignes de code dans `mon_scrappeur.rb` doivent être :
```ruby
require 'nokogiri'
require 'open-uri'
require 'pry'
```
Avec nokogiri on va pouvoir récuperer le contenu html de notre page. Open-uri nous permet d'ouvrir l'url de notre page cible. Et pry va nous servir a tester notre code sans le relancer a chaque fois.

#### 5.3.1 Extraction de la matière première
On va faire une methode `scraper` dans laquelle on va passer l'url d'indeed à nokogiri pour qu'il nous la fasse parler :
````ruby
def scraper
  # On met notre url dans une varialble
  url = "https://www.indeed.fr/emplois?q=D%C3%A9veloppeur%20Web%20ruby%20on%20rails&l=paris&vjk=3d896a7bb658f967"

  # On passe notre url à nokogiri et on met le tout dans une varialble 'page'
  page = Nokogiri::HTML(open("url"))

  # On utilise pry pour faire nos testes
  binding.pry
end

# On appel notre methode
scraper

````
Top ! Maintenant pour vérifier si cela fonctionne tu vas dans ton terminal, tu te mets à la racine de ton dossier `scrappeur` et tu lances le fichier `mon_scrappeur.rb` avec la command `$ ruby mon_scrappeur.rb`. Si tout va bien tu es maintenant dans l'IRB de pry. Pour tester ton code il te suffit de faire un petit `$ puts page`. Pas de panique tout est normale, ce qui s'affiche n'est que le code HTML de notre url, c'est ce qui va nous servir de matière première pour la suite !

#### 5.3.2 Usinage
Maintenant que nokogiri nous a bien maché le travail c'est au tour de Xpath de faire son boulot. Cependant Xpath a besoin d'un petit coup de main ! Tu vas chercher le bon path (chemin) qui te mène à l'information que tu veux.
Il y a plusieur façon pour trouver le path que tu cherches et [celle là](https://www.youtube.com/watch?v=2rlMNJa_4Z0) est plutôt rapide et assez explicite. Mais l'idéal c'est de fouiller le code sources de la page ! Imaginons que tu cherches le nom des entreprises qui ont posté des annonces sur cette page. Commence par créer une variable `companies_names` dans ta methode `scraper` :
````ruby
companies_names = page.xpath('le_fameux_path')
````
Avec la methode `xpath()` tu vas parser le code HTML retourné par nokogiri dans la variable `page`. Pour le path, si tu ne l'as pas trouvé le voici `//span[@class="company"]` . Avec ça tu demandes à Xpath de te pointer les balises HTML `<span>` qui ont la `class='company'`.
Maintenant relance `$ ruby mon_scrappeur.rb` et une fois dans pry vérifie la variable que tu viens de créer avec un `puts companies_names.text`. Magnifique non ? Tente un `puts companies_names.size` pour avoir le nombre d'entreprise parsées.
Je te laisse faire la même chose avec les salaires proposé par exemple.

#### 5.3.3 Produit finit
Une fois les informations chinées et triées sur le volet elles sont desormais ta propriété et tu peux en faire ce que tu veux. Tu peux créer un bot qui les stocke dans un GoogleSheet et qui recommence tous les mois pour voir l'évolution par exemple. Tu peux aussi aller sraper les pages d'amazon pour faire de la veille sur les prix des produits qui t'intéressent... Il n'y a vraiment pas de limite !
Maintenant à toi de jouer.
