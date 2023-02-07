# Atelier Solo : Triple Triad Deck Builder

**Feedback**: Jean-Baptiste

## Etape 1 - Détail d'une carte

L'ensemble des fonctionnalités demandées ne fonctionnent pas lors du click sur le lien d'une carte: Pourquoi ?

- Affichage: Le lien HTML de la carte ne se ferme pas correctement ce qui fait que le lien n'est pas actif sur l'ensemble de la carte mais que sur son nom.

```html
<div class="card">
  <a>Nom de la carte</a>
    <!-- le lien de doit pas se fermer ici car il se ferme déjà plus bas -->
    <img />
  </a>
</div>
```

- L'erreur **card is not defined** car la methode `render()` n'envoie pas card mais oneCard. Voici ce que nous aurions pu faire pour résoudre l'erreur:

```javascript
response.render("main/cardDetails", { card: oneCard });
//le fait de préciser la clé de l'objet envoyé lui donne un nom utilisable dans la vue.

// Exemple:
response.render("main/cardDetails", { oneCard });
// Est strictement égal à :
response.render("main/cardDetails", { oneCard: oneCard });
//On a affilié la valeur de oneCard à la clé oneCard !
```

- La vue _cardDetails.ejs_ est sencé n'afficher qu'une seule carte. Il existe cependant une boucle pour lister plusieurs éléments contenus dans card.

Rappel d'une boucle for dans une vue **EJS**

```javascript
//array = {name: "Doe", firstname: "John"}
<% for (const key of array) { %>
  <p>Je m'appelle <%= key.firstname %> <%= key.name %></p>
<% } %>
```

Pour corriger l'erreur de notre application, il suffit de supprimer la boucle for de la vue et le tour est joué :)

## Etape 2 - Recherche

La recherche par élement ne peut pas encore fonctionner comme elle est, plusieurs points sont à revoir:

- Le fichier dataMapper n'est pas inclut dans le fichier `searchController`
- La méthode dataMapper.cardElement() n'existe pas sous ce nom, elle s'appelle dataMapper.getCardByElement()
- Comme pour l'étape 1, le render ne fournit pas le bon tableau d'éléments
- Le HTML de la vue _main/element.ejs_ ne correspond pas à un résultat de recherche.
- Le requête dans le dataMapper sélectionne uniquement les cartes ayant un élément non NULL

```javascript
//importer un fichier
const dataMapper = require("../dataMapper");

//pas besoin ici de préciser l'extension du fichier, node s'occupe de ça tout seul.
```

Explication de la requete

```javascript
//dans le fichier dataMapper

getCardsByElement: async function (element) {
    let query;

    //Si le terme de recherche est strictement égal à 'null', alors on sélectionne les éléments dont l'élément est enregistré dans la table avec la valeur NULL
    if (element === 'null') {
      query = {
        text: `SELECT * FROM "card" WHERE "element" IS NULL`
      };

    } else {
      //Dans le cas contraire, element ne vaut pas 'null', alors on peut rechercher dans la table les cartes qui ont cette valeur.
      query = {
        text: `SELECT * FROM "card" WHERE "element"=$1`,
        values: [element]
      };

    }

    const results = await database.query(query);
    return results.rows;
  },
```

## Etape 3 - Construire un Deck

La construction du Deck n'est pas implémentée.

## Remarques générales

Les notions vues en cours sont à revoir. Je t'invite à suivre les liens que je te mets en bas de page et nous en discuterons ensemble !

> Nous verrons également les messages d'erreur que l'on peut rencontrer dans ce type de projet et comment les corriger au fur et à mesure. Il me semble que c'est la chose qui te fera le plus progresser et tu pourras avancer sereinement vers des principes plus complexes et réaliser de belles applications !

## Liens utiles

- [Anatomie d'un élément HTML](https://developer.mozilla.org/fr/docs/Learn/HTML/Introduction_to_HTML/Getting_started)
- [Importer un fichier javascript](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Statements/import)
