# MCD E-Commerce - Mugs O'Clock

## Résumé textuel des relations

### USER

- Un utilisateur peut se créer un compte mais n'est pas obligé
- Un utilisateur qui a créé un compte peut y ajouter une ou plusieurs adresses postales (adresse de livraison et adresse de facturation par exemple)
- Un utilisateur peut passer une ou plusieurs commandes
- Un utilisateur peut "liker" un ou plusieurs produits

### ADDRESS

- Une adresse ne peut être associée qu'à un seul utilisateur

### ORDER

- Une commande ne peut être associée qu'a un seul utilisateur
- Une commande peut contenir un ou plusieurs produits

### PRODUCT

- Un produit peut être dans plusieurs commandes
- Un produit peut être "liké" par un ou plusieurs utilisateurs

## Cardinalités

L'entité **USER HAS ADDRESS** a la bonne cardinalité **0,N** car il peut y avoir aucun ou une infinité d'adresses par utilisateur sur notre application.

L'entité **ADDRESS HAS USER** a la bonne cardinalité **1,1** car une adresse ne peut être associée qu'à un seul utilisateur. Une adresse a forcément un utilisateur associé.

L'entité **USER GENERATE ORDER** a la bonne cardinalité **0,N** car il peut y avoir aucune ou une infinité de commandes passées par utilisateur.

L'entité **ORDER GENERATE USER** a la bonne cardinalité **1,1** car une commande ne peut être associée qu'à un seul utilisateur.

L'entité **ORDER CONTAIN PRODUCT** a la bonne cardinalité **1,N** car une commande peut contenir une infinité de produits mais **1** au minimum.

L'entité **PRODUCT CONTAIN ORDER** a la bonne cardinalité **0,N** car un produit n'est pas forcément associé à une commande et il peut y avoir une infinité de produits par commande.

L'entité **USER LIKE PRODUCT** n'a pas la bonne cardinalité **1,1** car un utilisateur peut liker ou non qu'un seul produit à la fois mais n'est pas obligé. La bonne cardinalité serait **0,1**

L'entité **PRODUCT LIKE USER** n'a pas la bonne cardinalité **1,1** pour la même raison que USER LIKE PRODUCT. Un produit peut être liké qu'une seule fois par utilisateur mais n'est pas requis. La bonne cardinalité serait **0,1**

## Relations

La relation **Many to Many** pour **USER LIKE PRODUCT** est bonne car un utilisateur peut liker ou non un ou plusieurs produits et un produit peut être liké ou non par un ou plusieurs utilisateurs.

# Conclusion

Ce MCD est très compréhensible à vue d'oeil et je pense qu'on peut commencer le développement très bientôt !

On voit que malgré la toute petite erreur sur la cardinalité **USER LIKE PRODUCT**, le diagramme est compris et que tu sais exactement comment agencer les futures données. Bravo !

> Personnelement j'aime utiliser [Mocodo](https://www.mocodo.net/), un modélisateur de données très simple et en ligne, très pratique pour remplacer la feuille de papier volante !
