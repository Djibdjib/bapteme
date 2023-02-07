# Atelier Solo : Triple Triad Deck Builder

**Feedback**: Jean-Baptiste

## Etape 1 - Détail d'une carte

L'ensemble des fonctionnalités ne sont pas visibles lors de la navigation sur l'application.

Voyons le code:

- La route pour afficher le détail de la carte a été créée mais ne peut pas prendre de parametre dynamique dans son URL.

```javascript
//Cette route est dynamique et peut prendre un parametre via son URL: card/22 par exemple
router.get("card/:id", mainController.cardPage);

//Cette route n'est pas dynamique
router.get("card", mainController.cardPage);
```

- La méthode cardPage pour afficher le détail de la carte a été créée mais ne peut pas récupérer de parametre dynamiques

```javascript
//Le parametre dynamique peut se récupérer depuis le controlleur comme ceci, depuis l'objet request passé en parametre de la méthode.
const mainController = {
  cardPage: async (req, res) => {
    const cardId = parseInt(req.params.id, 10);
    console.log(cardId);
  },
};
//Nous avons utilisé req.params.id car dans la route nommée, nous avons indiqué :id.
//Nous aurions pu appeler le parametre autrement, par exemple :monparam que nous aurions récupéré comme ceci:
const cardId = req.params.monparam;
```

- La vue _card.ejs_ est identique à la vue _cardList_ et ne peut donc pas afficher le détail de la carte.

## Etape 2 - Recherche

Etape non implémentée

## Etape 3 - Construire un Deck

Etape non implémentée

## Remarques générales

> Je pense que je n'ai pas réussi à bien t'expliquer les bases du MVC (Model Vue Controller) dans un environnement javascript. Je te mets ci-dessous quelques liens utiles qui t'aiderons à mieux comprendre le squelette d'une application de type MVC.

## Liens utiles

- [ExpressJs - Les routes](https://expressjs.com/en/guide/routing.html)
- [Mini tutoriel - Routes et templates Ejs](https://www.digitalocean.com/community/tutorials/how-to-use-ejs-to-template-your-node-application)
