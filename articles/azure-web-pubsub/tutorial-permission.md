---
title: 'Tutoriel : Ajouter l’authentification et des autorisations à votre application lors de l’utilisation du service Azure Web PubSub'
description: Tutoriel montrant comment ajouter l’authentification et des autorisations à votre application lors de l’utilisation du service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: cca4951aac8844c13f36ebb58f8c326cec7b6104
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117368"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub-service"></a>Tutoriel : Ajouter l’authentification et des autorisations à votre application lors de l’utilisation du service Azure Web PubSub

Dans le [tutoriel Créer une application de conversation](./tutorial-build-chat.md), vous avez appris à utiliser des API WebSocket pour envoyer et recevoir des données avec Azure Web PubSub. Vous avez peut-être remarqué que, pour rester simple, cela ne nécessite aucune authentification. Même si Azure Web PubSub nécessite un jeton d’accès pour la connexion, l’API `negotiate` que nous avons utilisée dans le tutoriel pour générer un jeton d’accès n’a pas besoin d’authentification, de sorte que tout le monde peut appeler cette API pour obtenir un jeton d’accès.

Dans une application réelle, il est courant que vous souhaitiez que l’utilisateur se connecte avant de pouvoir utiliser votre application pour la protéger contre un abus. Dans ce tutoriel, vous allez apprendre à intégrer Azure Web PubSub au système d’authentification/d’autorisation de votre application pour la rendre plus sécurisée.

L’exemple de code complet de ce tutoriel est disponible [ici][code].

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer l’authentification GitHub
> * Ajouter un middleware (intergiciel) d’authentification à votre application
> * Ajouter des autorisations aux clients

## <a name="add-authentication-to-the-chat-room-app"></a>Ajouter l’authentification à l’application de la salle de conversation

Ce tutoriel réutilise l’application de conversation créée dans le [tutoriel Créer une application de conversation](./tutorial-build-chat.md). Vous pouvez également cloner l’exemple de code complet pour l’application de conversation à partir d’[ici][chat-js]. 

Dans ce tutoriel, nous allons ajouter l’authentification à l’application de conversation et l’intégrer au service Azure Web PubSub.

Tout d’abord, nous allons ajouter l’authentification GitHub à la salle de conversation afin que l’utilisateur puisse utiliser un compte GitHub pour se connecter.

1.  Installer des dépendances

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

2.  Ajoutez le code suivant à `server.js` pour activer l’authentification GitHub.

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    Le code ci-dessus utilise [Passport.js](http://www.passportjs.org/) pour activer l’authentification GitHub. Voici une illustration simple de son fonctionnement :

    1. `/auth/github` effectue une redirection vers github.com pour la connexion.
    2. Une fois la connexion effectuée, GitHub vous redirige vers `/auth/github/callback` avec un code permettant à votre application d’effectuer l’authentification (le rappel de vérification dans `passport.use()` montre comment le profil retourné à partir de GitHub est vérifié et conservé sur le serveur).
    3. Une fois l’authentification effectuée, vous êtes redirigé vers la page d’accueil (`/`) du site.
 
    Pour plus d’informations sur GitHub OAuth et Passport.js, consultez les articles suivants :

    - [Autorisation des applications OAuth](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Documentation Passport.js](http://www.passportjs.org/docs/)

    Pour tester cela, vous devez d’abord créer une application GitHub OAuth :

    1. Accédez à https://www.github.com, ouvrez votre profil -> Settings (Paramètres) -> Developer settings (Paramètres de développement).
    2. Accédez à OAuth Apps (Applications OAuth), puis cliquez sur « New OAuth App » (Nouvelle application OAuth).
    3. Renseignez le nom de l’application, l’URL de la page d’accueil (ce que vous souhaitez) et définissez l’URL de rappel d’autorisation sur `http://localhost:8080/auth/github/callback`(qui correspond à l’API de rappel que vous avez exposée sur le serveur).
    4. Une fois l’application inscrite, copiez l’ID client et cliquez sur « Generate a new client secret » (Générer un nouveau secret client) pour générer un nouveau secret client.

    Ensuite, exécutez `node server <connection-string> <client-id> <client-secret>` et ouvrez `http://localhost:8080/auth/github` ; vous êtes alors redirigé vers GitHub pour vous connecter. Une fois la connexion réussie, vous êtes redirigé vers l’application de conversation.

3.  Nous allons ensuite mettre à jour la salle de conversation pour utiliser l’identité que nous avons obtenue à partir de GitHub, au lieu d’afficher une boîte de dialogue pour demander le nom d’utilisateur.

    Mettez à jour `public/index.html` pour appeler directement `/negotiate` sans passer un identifiant utilisateur.

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    Quand un utilisateur est connecté, la demande utilise automatiquement l’identité de l’utilisateur par le biais d’un cookie. Nous devons simplement vérifier si l’utilisateur existe dans l’objet `req` et ajouter le nom d’utilisateur au jeton d’accès Web PubSub :

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    À présent, réexécutez le serveur ; un message de type « non autorisé » s’affiche la première fois que vous ouvrez la salle de conversation. Cliquez sur le lien de connexion pour vous connecter ; vous verrez qu’elle fonctionne comme avant.

## <a name="working-with-permissions"></a>Utilisation des autorisations

Dans les tutoriels précédents, vous avez appris à utiliser `WebSocket.send()` pour publier directement des messages sur d’autres clients avec un sous-protocole. Dans une application réelle, vous pouvez ne pas souhaiter que le client soit en mesure de publier sur un groupe, ou de s’y abonner, sans contrôle d’autorisation. Dans cette section, vous allez apprendre à contrôler les clients avec le système d’autorisation d’Azure Web PubSub.

Dans Azure Web PubSub, il existe trois types d’opérations qu’un client peut faire avec un sous-protocole :

- Envoyer des événements au serveur
- Publier des messages dans un groupe
- Se joindre (s’abonner) à un groupe

L’envoi d’un événement au serveur est l’opération par défaut du client même si aucun protocole n’est utilisé ; il est donc toujours autorisé. Pour publier sur un groupe et s’y abonner, le client doit obtenir une autorisation. Il existe deux façons pour le serveur d’accorder l’autorisation aux clients :

- Spécifier les rôles quand un client est connecté (le rôle est un concept qui représente les autorisations initiales quand un client est connecté)
- Utiliser une API pour accorder une autorisation à un client après sa connexion

Une fois qu’il a obtenu l’autorisation de rejoindre un groupe, le client ne peut le faire qu’en utilisant un message prévu à cet effet. Ou le serveur peut utiliser l’API pour ajouter le client à un groupe même s’il ne dispose pas de l’autorisation de le rejoindre.

Utilisons maintenant ce système d’autorisation pour ajouter une nouvelle fonctionnalité à la salle de conversation. Nous allons ajouter un nouveau type d’utilisateur appelé administrateur à la salle de conversation. Nous allons autoriser l’administrateur à envoyer un message système (le message commence par « [SYSTEM] ») directement à partir du client.

Tout d’abord, nous devons séparer les messages système et les messages utilisateur en deux groupes différents afin que leurs autorisations puissent être contrôlées séparément.

Changez `server.js` pour envoyer les différents messages aux différents groupes :

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

Vous pouvez voir que le code ci-dessus utilise `WebPubSubServiceClient.group().sendToAll()` pour envoyer un message au groupe au lieu du hub.

Étant donné que le message est désormais envoyé à des groupes, nous devons ajouter des clients aux groupes afin qu’ils puissent continuer à recevoir des messages. Cette opération est effectuée dans le gestionnaire `handleConnect`.

> [!Note]
> `handleConnect` est déclenché quand un client tente de se connecter à Azure Web PubSub. Dans ce gestionnaire, vous pouvez retourner des groupes et des rôles, de sorte que le service peut ajouter une connexion à des groupes ou accorder des rôles, dès que la connexion est établie. Il peut également `res.fail()` refuser la connexion.
>

Pour que `handleConnect` soit déclenché, accédez aux paramètres du gestionnaire d’événements dans le portail Azure et vérifiez `connect` dans les événements système.

Nous devons également mettre à jour le code HTML du client, car le serveur envoie désormais des messages JSON au lieu de texte brut :

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

Changez ensuite le code client à envoyer au groupe système quand l’utilisateur clique sur « system message » :

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

Le client n’ayant pas, par défaut, l’autorisation d’envoi à un groupe, mettez à jour le code du serveur pour accorder l’autorisation à l’utilisateur administrateur (par souci de simplicité, l’ID de l’administrateur est fourni en tant qu’argument de ligne de commande).

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

À présent, exécutez `node server <connection-string> <client-id> <client-secret> <admin-id>` ; vous verrez que vous pouvez envoyer un message système à chaque client quand vous vous connectez en tant que `<admin-id>`.

Toutefois, si vous vous connectez en tant qu’utilisateur différent, quand vous cliquez sur « system message », rien ne se produit. Vous pouvez vous attendre à ce que le service vous indique une erreur pour vous informer que l’opération n’est pas autorisée. Pour configurer cette fonctionnalité, vous pouvez définir un `ackId` lors de la publication du message. Chaque fois que `ackId` est spécifié, Azure Web PubSub retourne un message d’accusé de réception avec un `ackId` correspondant pour indiquer si l’opération a réussi ou non.

Remplacez le code d’envoi du message système par le code suivant :

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

Changez également le code de traitement des messages de manière à gérer le message d’accusé de réception :

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

À présent, réexécutez le serveur et connectez-vous en tant qu’utilisateur différent ; un message d’erreur s’affiche quand vous tentez d’envoyer un message système.

L’exemple de code complet de ce tutoriel est disponible [ici][code].

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous donne une idée de base de la façon de se connecter au service Web PubSub et de publier des messages sur les clients connectés en utilisant un sous-protocole.

Pour plus d’informations sur l’utilisation du service, consultez les autres tutoriels.

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
