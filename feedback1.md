# Atelier Solo : Triple Triad Deck Builder

**Feedback**: Jean-Baptiste

## Initialisation

La variable d'environnement SESSION*SECRET n'a pas été définie dans le fichier `.env` et est utilisée dans le fichier \_index.js*. La clé secrète de la la session est alors définie avec la valeur _undefined_.

```
SESSION_SECRET=masessionsecretequepersonnededoitvoir
```

```javascript
//Utilisation dans un fichier javascript
process.env.SESSION_SECRET;
```

## Etape 1 - Détail d'une carte

Le code fonctionne comme demandé, je note que la page 404 est bien appelée lorsqu'un _id_ inconnu est rentré dans l'URL, bravo !

### Améliorations

- Le nom de la méthode demandée pour récupérer les infos d'une carte est `getCard` et non `getOneCard`.

- Dans un contexte de **_cleanCode_**, il est plus explicite et plus facilement lisible de:

  - Nommer la méthode `getCards` pour sélectionner l'ensemble des cartes
  - Nommer la méthode `getCard` (sans le s) pour sélectionner une carte.

- Il est également recommandé de préciser un attribut `title` sur les liens HTML afin d'optimiser le **référencement naturel**, comme tu l'as très bien fait sur les balises _img_ avec l'attribut `alt` !

- Dans ton fichier card-item, la nomenclature HTML voudrait qu'il n'y ait pas que des balises _p_ mais aussi une liste à puce comme dans la correction.

## Etape 2 - Recherche

Le code fonctionne comme demandé à part une petite coquille:

**Ligne 22 du fichier searchController:**

```javascript
title: 'Résultat de la recherche : ' + (searchedElement === 'null' ? ' sans élément' : + searchedElement),
```

Le signe + s'est invité à la fête et affiche un _NaN_ (Not a Number) dans le titre lorsque la demande n'est pas nulle.

Dans ce cas de figure, javascript essaye de **calculer** la valeur searchedElement (avec l'opérateur +). Il ne peut le faire avec la chaine de caractères transmise par le formulaire.

### Améliorations

- L'écriture d'une petite requête simple comme dans ta méthode `searchByElement` peut s'écrire sur une seule ligne afin d'améliorer la lisibilité et rendre le fichier sur moins de lignes (voir la correction).

- Toujours dans le contexte de **cleanCode**, le nom de la méthode `getCardsByElement` est plus judicieux que `searchByElement` car on comprend davantage à la lecture qu'il s'agit bien ici d'une recherche sur les cartes et non d'une recherche générique.

## Etape 3 - Construire un Deck

J'aime beaucoup l'idée du middleware pour la gestion de la session ! il permet de rendre l'application mieux agencée et offre des options d'optimisation plus poussées.

Le code fonctionne comme demandé mais des améliorations sont possibles (voir plus bas).

### Améliorations

- Il aurait été judicieux de créer un nouveau fichier contrôleur `deckController` afin de rendre la le code plus logique et propre. Chaque fichier a son rôle bien défini et on doit pouvoir comprendre directement à la lecture qu'il existe des actions concernant le Deck, toutes bien rangées dans le dit fichier.

- Tu appelles la vue _card/deck_ pour l'affichage des cartes du deck alors qu'il existe déjà une vue _main/cardList_. Que se passe-t-il si maintenant on souhaite modifier la carte (son style ou bien sa mise en page HTML) ? nous sommes dans ce cas obligé de modifier la carte à deux endroits à la fois, ce qui n'est pas très pratique.  
  Le but est d'avoir une seule vue pour la carte et au besoin y intégrer des conditions selon certains paramètres qu'on lui fournirait depuis le contrôleur. Par exemple vérifier si la carte est dans le deck et afficher les boutons [ + ] ou [ - ] selon le cas.

```javascript
//On teste si les cartes séléctionnées sont dans la session
cards.forEach((card, k) => {
  cards[k].isDeckInside = req.session.deck.find(
    (current) => current.id === card.id
  );
});

//Puis on passe les informations à la vue
res.render("main/cardList", {
  cards,
  title: "Mon titre",
});
```

On peut alors faire une condition dans la vue:

```html
<% if (card.isDeckInside === true) { %>
<p>Je suis dans le deck !</p>
<% } else { %>
<p>Je ne suis pas dans le deck</p>
<% } %>
```

- Pour l'ajout d'une carte au deck, tu vérifies en premier l'existence de la carte en base de données pour ensuite vérifier l'existence de la carte dans la session servant au Deck. Même si la technologie aujourd'hui permet de traiter rapidement des milliers de données, il sera toujours plus optimal de vérifier d'abord l'existence de la carte en session qui en contiendra au maximum 5, contre 110 dans notre cas en base de données.

- Il y a des `console.log` non commentés dans le code. Il est préférable de les mettre en commentaire afin de ne pas encombrer la console.

## BONUS - Finir les recherches

- La recherche par niveau fonctionne comme demandé.

- La recherche par valeur fonctionne de manière exacte alors que nous voulons récupérer les cartes qui ont _au moins_ la valeur indiquée. Il faut utiliser l'opérateur `>=` dans la requête pour sélectionner les cartes qui ont une valeur **supérieure ou égale**.

- La recherche par nom fonctionne très bien et tu as même classé les résultats par Id !

## Remarques générales

L'exercice a été réalisé avec succès et l'ensemble des consignes a été respecté alors bravo !

> Les points d'améliorations cités ci-dessus sont à travailler au quotidien car ils touchent à l'organisation du code. Plus d'organisation = moins de _dette technique_. En effet, l'application **Triple Triad** est petite mais il faut toujours garder en tête qu'elle peut un jour devenir plus grande et plus profonde.

## Lien(s) utile(s)

- [C'est quoi le Clean Code ?](https://www.ionos.fr/digitalguide/sites-internet/developpement-web/clean-code-avantages-principes-et-exemples/)
