# Fetch

## Oh fetch ! C'est quoi ?

[Fetch](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch) est une méthode utilitaire javascript, native à l'ensemble des navigateurs du marché, servant à fournir un moyen de récupérer des ressources (des données provenant d'une API ou envoyer des fichiers sur le réseau par exemple) de manière asynchrone.

Cet utilitaire ne dépend pas de l'installation d'une librairie tiers pour pouvoir fonctionner (comme la librairie Axios par exemple). Elle est **native**.

## Comment ça marche ?

```javascript
const url = "/login";
const options = {};

const promise = fetch(url, options);

promise
  .then((response) => {
    console.log(response);
  })
  .catch((error) => console.error(error));
```

## Particularité de la réponse

Fetch ne retourne pas le résultat au format JSON automatiquement. Ce retour doit être transformé au format voulu manuellement (format JSON par exemple, mais aussi au format blob pour une image par exemple)

Exemples:

```javascript
const url = "/login";
const options = {};

const promise = fetch(url, options);

//La réponse est transformée au format JSON ici
promise
  .then((response) => response.json())
  //On peut maintenant, en cas de succès, accéder au graal, nos données JSON
  .then((data) => {
    console.log(data);
  })
  .catch((error) => console.error(error));
```

Pour une image au format _blob_

```javascript
const myImage = document.querySelector("img");

const promise = fetch("flowers.jpg");

promise
  .then(function (response) {
    //La réponse est traitée au format blob
    return response.blob();
  })
  .then(function (myBlob) {
    const objectURL = URL.createObjectURL(myBlob);
    myImage.src = objectURL;
  });
```

## Les options

Fetch prend comme second paramètre un objet d'options facultatives:

```javascript
const options = {
  //méthode d'envoi
  method: "post",
  //Entêtes de la requète
  headers: {
    // on définit le format du body.
    "Content-Type": "application/json",
    // Nous n'accepterons que le JSON en résultat.
    Accept: "application/json",
    // Cas d'usage courant pour gérer l'authentification avec une API REST et token JWT.
    Authorization: "Bearer ${token}",
  },
  //corp des données envoyées
  body: JSON.stringify({ name: "Doe", firstname: "John" }),
};

//... fetch(url, options);
```

d'autres options sont disponibles sur la [documentation officielle](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch).

## Requètes multiples simultanées

Grâce à la méthode native **Promise** nous pouvons gérer les requètes simultanées et ainsi rendre notre code plus logique selon nos besoins:

```javascript
Promise.all([fetch("/url/vers/mon/api_1"), fetch("/url/vers/mon/api_2")])
  .then(async ([res1, res2]) => {
    const a = await res1.json();
    const b = await res2.json();

    console.log(a); //données de la requète 1
    console.log(b); //données de la requète 2
  })
  .catch((error) => {
    console.log(error);
  });
```

## Exemple concret pour l'application Triple Triad

Nous pouvons maintenant utiliser fetch dans notre application pour appeler de manière asynchrone toutes nos cartes sur la page d'accueil. Nous éviterons ainsi d'attendre la sélection des cartes depuis le serveur avant d'afficher la page.

Il nous faut créer une nouvelle route et ajouter un nouveau controller `apiController`:

```javascript
//router.js
const apiController = require("./controllers/apiController");

router.get("/api/cards", apiController.getCards);
```

Voici le contenu de `apiController`

```javascript
//controllers/apiController.js
const dataMapper = require("../dataMapper.js");

const apiController = {
  getCards: async (req, res) => {
    try {
      const cards = await dataMapper.getAllCards();

      res.status(200).json(cards ?? {});
    } catch (error) {
      console.error(error);
      res
        .status(500)
        .send(`An error occured with the database :\n${error.message}`);
    }
  },
};

module.exports = apiController;
```

Nous avons maintenant une nouvelle URL accessible ici: [http://localhost:1234/api/cards](http://localhost:1234/api/cards)

Cette URL doit nous retourner une réponse au format JSON, contenant nos cartes.

### Particularité de notre application.

Notre application compile sur le serveur NodeJs l'ensemble du HTML. Il n'y a donc pas de processus de **virtual DOM** et le rendu HTML ne peut être rendu _à la volée_. C'est pour cela que nous ne pouvons pas nous contenter du format JSON généré par notre nouvelle route: nous devons la modifier pour rendre du HTML généré avec EJS.

Modification du controller `apiController`

```javascript
const dataMapper = require("../dataMapper.js");

const apiController = {
  getCards: async (req, res) => {
    try {
      const cards = await dataMapper.getAllCards();

      // Nous générons le HTML des cartes avec EJS depuis une nouvelle vue
      res.render("card/card", { cards });
    } catch (error) {
      console.error(error);
      res
        .status(500)
        .send(`An error occured with the database :\n${error.message}`);
    }
  },
};

module.exports = apiController;
```

Création d'une nouvelle vue qui va afficher uniquement les cartes

```html
<div class="cardsContainer">
  <% cards.forEach( (card) => { %>

  <div class="card">
    <a href="/card/<%= card.id %>" title="<%= card.name %>">
      <img
        src="/visuals/<%= card.visual_name %>"
        alt="<%= card.name %> illustration"
      />
      <p class="cardName"><%= card.name %></p>
    </a>
    <a
      class="link--addCard"
      title="Ajouter au deck"
      href="/deck/add/<%= card.id %>"
      >[ + ]</a
    >
  </div>

  <% }) %>
</div>
```

L'URL de notre route `api/cards` affiche désormais les cartes au format HTML.

### Mise en place de Fetch sur le client

Pour avoir un rendu **asynchrone**, fetch doit être appelé depuis le client, et non le serveur. D'ailleurs, fetch est un utilitaire javascript coté _client_. Pour utiliser fetch sur un serveur NodeJs, nous devons installer le package **node-fetch** (pas dans notre cas ici).

Modification de la vue _cardList.ejs_

```html
<%- include('partials/header') %>

<h1><%= title %></h1>

<div class="cardsContainer">
  <script>
    const url = "/api/cards";

    window.addEventListener("load", () => {
      //Appel à fetch
      const promise = fetch(url);

      promise
        //On veut une réponse au format texte
        .then((response) => response.text())
        .then((data) => {
          const cards = document.getElementById("cards");
          //Insertion du contenu des cartes au format HTML
          cards.innerHTML = data;
        })
        .catch((error) => console.error(error));
    });
  </script>

  <!-- la div qui contiendra le contenu des cartes généré par notre api -->
  <div id="cards"></div>
</div>

<%- include('partials/footer') %>
```

Nous exécutons ici notre appel **fetch** au moment ou la page est chargée grâce à `window.addEventListener("load")`. Une fois la réponse reçue, nous la transformons au format texte avec `response.text()`. Nous pouvons maintenant insérer les cartes reçues par notre api dans la div `cards`.

## Conclusion

Nous savons maintenant comment faire un appel asynchrone à des données avec l'utilitaire fetch, depuis le client.

> Ce procédé n'est pas à prendre à la légère et son impact doit être réfléchi en amont lors de la conception de notre application. En effet, les rendus générés à la volée ne sont plus dans le code source de la page et il peut y avoir des répercussions sur le référencement naturel de l'application.
