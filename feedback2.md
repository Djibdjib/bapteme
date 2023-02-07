# Atelier Solo : Triple Triad Deck Builder

**Feedback**: Jean-Baptiste

## Etape 1 - Détail d'une carte

- Sur la page détail d'une carte, il manque quelques informations:

  - l'élement
  - Les valeurs cardinales

- Il manque la vérification de l'existence de la carte dans la base de données lorsqu'on met dans l'URL un id de carte qui n'existe pas. La page 404 devrait s'afficher dans ce cas la.

Ci dessous un exemple qui teste si une carte a été trouvée et qui fait le bon renvoi.

```javascript
try {
  //On sélectionne la carte grâce à son id cardId
  const card = await dataMapper.getOneCard(cardId);

  //On attend le retour de card (grâce à await dans une fonction asynchrone)
  if (card) {
    //La carte a été trouvée, on affiche la page
    response.render("cardDetails", { card });
  } else {
    //Carte introuvable, on affiche la page 404
    response.status(404).send(`Carte ${cardId} introuvable`);
  }
} catch (error) {
  //erreur importante, on debug avec console.log et on renvoie une erreur 500
  console.error(error);
  response.status(500).render("error");
}
```

Le reste de l'étape, à savoir la création de la route paramétrée et du controleur est très bien.

Je met un plus pour le choix d'un contrôleur spécifique `cardController` afin d'être mieux organisé.

## Etape 2 - Recherche

La recherche par élément fonctionne ! dommage pour l'affichage de la carte qui n'a pas été repris comme pour la _Home page_.

Cependant, la requête n'est pas optimisée car elle sélectionne des cartes qu'on ne veut pas forcément dans notre résultat de recherche, laissant le javascript traiter le tri. Il est beaucoup plus optimisé de trier directement avec la requête, se qui économise les ressources et établit un partage des tâches cohérent.  
D'une manière générale, il vaut mieux privilégier la liste blanche (ce qu'on veut) à la liste noire (ce qu'on ne veut pas) car le retour est davantage prévisible.

```sql
/* requete optimisée car elle tente de trouver ce qu'on lui demande */
SELECT * FROM card WHERE element="tonnerre"

/* requete non optimisée dans notre cas car elle est beaucoup trop générique et sélectionne des cartes qu'on ne veut pas forcément */
SELECT * FROM card WHERE element IS NOT NULL
```

## Etape 3 - Construire un Deck

- La page deck existe bien et est appelée depuis le controlleur `deckController` et la vue est au bon endroit _deck.ejs_.

- L'option d'ajout a bien été mise en place dans le `deckController` mais n'est pas limitée à 5.

- Il n'existe malheureusement pas l'option pour retirer une carte du Deck.

### Améliorations

- On pourrait cependant en premier lieu vérifier l'existence de la carte en session avant de vérifier si la carte existe bien en base de données pour économiser des appels à la base de données car il est toujours moins couteux de vérifier la session qui comporte au maximum 5 cartes que l'existence de la carte sur l'ensemble des cartes en base de données.

Le fait de vérifier la session en premier permet aussi de compter le nombre de cartes déjà présentes dans le Deck et de procéder à l'insertion uniquement si nous y sommes autorisé.

```javascript
//length permet de compter le nombre d'éléments d'un tableau
const cardsLength = req.session.deck.length;
if (cardsLength < 5) {
  //On peut continuer
}
```

Résumons:

1. Nous comptons le nombre de cartes dans le Deck
2. Si le nombre de carte est inférieur à notre limite de 5, nous contrôlons la présence en session de la carte qu'on souhaite ajouter.
3. Si la carte n'a pas déjà été ajoutée en session (donc dans notre Deck), nous vérifions si la carte existe en base de données.
4. Si la carte existe en base de données, nous pouvons l'ajouter à notre Deck (ajout dans la session)

Au niveau de la vue, lorsqu'il n'y a pas de carte encore dans notre Deck, afin de ne pas afficher les labels du tableau, on aurait pu séparer:

1. L'affichage du message disant qu'il n'y a pas de carte dans notre Deck
2. L'affichage du Deck

## BONUS - Finir les recherches

L'application ne bénéficie pas du bonus.

## Remarques générales

L'exercice n'a pas été terminé, peut-être es-ce du à un petit manque de temps ou d'organisation. C'est pour cela que nous reprendrons ce point lors de notre prochaine visio car nous pouvons voir que le procédé MVC (Model Vue Controller) est compris et que l'organisation des différents fichier est bonne.

> Il faut continuer de pratiquer dans ce sens !
