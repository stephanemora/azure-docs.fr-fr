---
title: Utilisation du widget du lecteur d’Azure Video Analyzer
description: Cet article de référence explique comment ajouter un widget du lecteur de Video Analyzer à votre application.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 05/11/2021
ms.openlocfilehash: f4c2d3f7d13002d0de231859bc31a74b72b1c7fd
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555125"
---
# <a name="using-the-azure-video-analyzer-player-widget"></a>Utilisation du widget du lecteur d’Azure Video Analyzer

Ce tutoriel explique comment utiliser le widget du lecteur d’Azure Video Analyzer dans votre application.  Ce code est un widget facile à incorporer, qui permet à vos utilisateurs finaux de lire des vidéos et de naviguer dans les différentes parties d’un fichier vidéo segmenté.  Pour ce faire, vous allez générer une page HTML statique avec le widget incorporé, et tous les éléments pour le faire fonctionner.

Dans ce tutoriel vous allez :

> [!div class="checklist"]
> * Créer un jeton
> * Répertorier les vidéos
> * Obtenir l’URL de base pour lire une [ressource d’application vidéo](./terminology.md#video)
> * Créer une page avec le lecteur
> * Transmettre le point de terminaison de diffusion en continu et le jeton au lecteur

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

- [Composants web](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :
* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur pour le fichier HTML.
one.
* [Enregistrement et lecture de vidéo continue](./use-continuous-video-recording.md) ou [Détecter les mouvements et enregistrer des vidéos sur des appareils de périphérie](./detect-motion-record-video-clips-cloud.md)

## <a name="create-a-token"></a>Créer un jeton

Dans cette section, nous allons créer un jeton JWT que nous utiliserons ultérieurement dans le document.  Nous allons utiliser un exemple d’application qui va générer le jeton JWT et vous fournir tous les champs requis pour créer la stratégie d’accès.

> [!NOTE] 
> Si vous savez comment générer un jeton JWT basé sur un certificat RSA ou ECC, vous pouvez ignorer cette section.

1. Téléchargez l’application JWTTokenIssuer située [ici](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/jwt-token-issuer/).

   > [!NOTE] 
   > Pour plus d’informations sur la configuration de vos valeurs d’audience, consultez cet [article](./access-policies.md)

2. Lancez Visual Studio Code et ouvrez le dossier contenant le fichier *.sln.
3. Dans le volet de l’explorateur, accédez au fichier program.cs
4. Modifiez la ligne 77 - modifiez l’audience de votre point de terminaison de Video Analyzer plus /videos/* afin qu’elle ressemble à ceci :

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```
5. Modifiez la ligne 78 - remplacez l’émetteur par la valeur de l’émetteur de votre certificat.  Exemple : https://contoso.com

   > [!NOTE] 
   > Le point de terminaison de Video Analyzer se trouve dans la section de vue d’ensemble de la ressource Video Analyzer dans Azure. Vous devez cliquer sur le lien « Affichage JSON » 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/endpoint.png" alt-text="Widget de lecteur – Point de terminaison":::
6. Enregistrez le fichier .
7. Appuyez sur `F5` pour exécuter l’application JWTTokenIssuer.

Cette opération a pour effet de générer et d’exécuter l’application.  Une fois celle-ci générée, elle s’exécute en créant un certificat via openssl.  Vous pouvez également exécuter le fichier JWTTokenIssuer.exe situé dans le dossier de debug.  L’avantage de l’exécution de l’application est que vous pouvez spécifier des options d’entrée comme suit :

- JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]

JWTTokenIssuer crée le jeton JWT et les composants requis suivants :

- `kty`; `alg`; `kid`; `n`; `e`

Veillez à copier ces valeurs pour une utilisation ultérieure.

## <a name="create-an-access-policy"></a>Définition d’une stratégie d’accès.

Des stratégies d’accès définissent les autorisations et la durée d’accès à un flux vidéo donné de Video Analyzer.  Pour les besoins de ce tutoriel, nous allons configurer une stratégie d’accès pour Video Analyzer dans le portail Azure.  

1. Connectez-vous au portail Azure et accédez au groupe de ressources où se trouve votre compte Video Analyzer.
1. Sélectionnez la ressource Video Analyzer.
1. Sous Video Analyzer, sélectionnez Stratégies d’accès

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Widget de lecteur – Stratégies d’accès au portail":::    
1. Cliquez sur nouveau, puis entrez ce qui suit :

   > [!NOTE] 
   > Ces valeurs proviennent de l’application JWTTokenIssuer créée à l’étape précédente.

   - Nom de la stratégie d’accès : n’importe quel nom

   - Émetteur : doit correspondre à l’émetteur du jeton JWT 

   - Audience : Audience du jeton JWT -- ${System.Runtime.BaseResourceUrlPattern} est la valeur par défaut.  Pour en savoir plus sur audience et ${System.Runtime.BaseResourceUrlPattern} , consultez cet [article](./access-policies.md)

   - Type de clé : kty -- RSA 

   - Algorithme : les valeurs prises en charge sont RS256, RS384, RS512

   - ID de clé : kid -- généré à partir de votre certificat

   - Valeur N : pour RSA, la valeur N est le Modulo

   - Valeur E : pour RSA, la valeur E est l’Exposant public

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Widget de lecteur – Portail des stratégies d’accès":::     
1. cliquez sur `save`.

## <a name="list-video-analyzer-video-resources"></a>Répertorier les ressources vidéo de Video Analyzer

Nous allons générer une liste de ressources vidéo.  Pour ce faire, nous adressons un appel REST au point de terminaison de compte que nous avons utilisé plus haut, qui a pour effet d’effectuer l’authentification avec le jeton que nous avons généré.

Il existe de nombreuses façons d’envoyer une requête GET à une API REST. En l’occurrence, nous allons utiliser une fonction JavaScript.  Le code ci-dessous utilise une requête [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) couplée aux valeurs que nous stockons dans les champs `clientApiEndpointUrl` et `token` de la page pour envoyer une requête `GET` synchrone.  Il prend ensuite la liste des vidéos obtenue, et stocke celles-ci dans la zone de texte `videoList` que nous avons configurée sur la page.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```

## <a name="add-the-video-analyzer-player-component"></a>Ajouter le composant Lecteur de Video Analyzer

Maintenant que nous disposons d’une URL de point de terminaison d’API client, d’un jeton et d’un nom de vidéo, nous pouvons ajouter le lecteur à la page.

1. Incluez le module lecteur proprement dit en ajoutant le package directement à votre page.  Vous pouvez inclure la direction du package NPM dans votre application ou faire en sorte de l’incorporer de façon dynamique au moment de l’exécution, comme ici :
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. Ajoutez un élément AVA-Player au document :
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. Obtenez un lien vers le widget du lecteur de Video Analyzer qui se trouve dans la page :
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. Pour configurer le lecteur avec les valeurs dont vous disposez, vous devez configurer celles-ci en tant qu’objet, comme ici :
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Charger la vidéo dans le lecteur pour commencer
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>Assemblage

Si nous combinons les éléments Web ci-dessus, nous obtenons la page HTML statique suivante, qui nous permettra d’utiliser un point de terminaison de compte et un jeton pour afficher une vidéo.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
</body>
</html>
```

## <a name="host-the-page"></a>Héberger la page

Vous pouvez tester cette page localement, mais vous souhaiterez peut-être tester une version hébergée.  Si vous n’avez pas de moyen rapide d’héberger une page, voici des instructions sur la façon de le faire en utilisant des [sites web statiques](../../storage/blobs/storage-blob-static-website.md) avec Stockage.  Vous trouverez ci-dessous une version condensée de [ces instructions plus complètes](../../storage/blobs/storage-blob-static-website-how-to.md) mises à jour pour les fichiers que nous utilisons dans ce tutoriel.

1. Créer un compte de stockage
1. Sous `Data management` à gauche, cliquez sur `Static website`
1. `Enable` le site web statique sur le compte de stockage
1. Pour `Index document name`, entrez `index.html`
1. Pour `Error document path`, entrez `404.html`
1. Sélectionnez `Save` en haut
1. Notez le `Primary endpoint` qui s’affiche : ce sera votre site web
1. Cliquez sur `$web` au-dessus de `Primary endpoint`
1. À l’aide du bouton `Upload` situé en haut, chargez votre page HTML statique en tant que `index.html`

## <a name="play-a-video"></a>Lire une vidéo

Maintenant que la page est hébergée, naviguez-y. Vous devriez être en mesure de suivre les étapes.

1. Entrez l’URL `Client API endpoint URL` et le jeton `Token`
1. Appuyez sur `Get videos`
1. Dans la liste de vidéos, sélectionnez un nom de vidéo et renseignez-le dans le champ d’entrée `Video name`
1. Appuyez sur `Play video`

## <a name="additional-details"></a>Informations supplémentaires

### <a name="refreshing-the-access-token"></a>Actualisation du jeton d’accès

Le lecteur utilise le jeton d’accès que nous avons généré précédemment pour obtenir un jeton d’autorisation de lecture.  Les jetons expirant périodiquement, vous devez les actualiser.  Il existe deux manières d’actualiser le jeton d’accès pour le lecteur après en avoir généré un nouveau.

* En appelant activement la méthode de widget `setAccessToken`
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* En agissant sur l’événement `TOKEN_EXPIRED` en écoutant cet événement
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

L’événement `TOKEN_EXPIRED` se produit 5 secondes avant l’expiration du jeton.  Si vous définissez un détecteur d’événements, nous vous recommandons de le faire avant d’appeler la fonction `load` sur le widget du lecteur.

### <a name="configuration-details"></a>Détails de configuration

Nous avons effectué une configuration simple pour le lecteur ci-dessus, mais elle prend en charge un éventail plus large d’options pour les valeurs de configuration.  Les champs pris en charge sont les suivants :

| Nom   | Type             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| token  | string | Votre jeton JWT pour le widget |
| videoName | string | Nom de la ressource vidéo  |
| clientApiEndpointUrl | string | URL de point de terminaison pour l’API client |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>Autres façons de charger le code dans votre application

Le package utilisé pour récupérer le code dans votre application est un package NPM [ici](https://www.npmjs.com/package/video-analyzer-widgets).  Dans l’exemple ci-dessus, la version la plus récente a été chargée au moment de l’exécution directement à partir du référentiel. Vous pouvez également télécharger et installer le package localement comme suit :

```bash
npm install @azure/video-analyzer/widgets
```

Ou bien, vous pouvez l’importer dans le code de votre application comme suit pour Typescript :

```typescript
import { Player } from '@video-analyzer/widgets';
```

Ou pour JavaScript, si vous souhaitez créer un widget de lecteur dynamiquement :
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

Si vous utilisez cette méthode pour importer, vous devrez créer par programmation l’objet lecteur une fois l’importation terminée.  Dans l’exemple ci-dessus, vous avez ajouté le module à la page à l’aide de la balise HTML `ava-player`.  Pour créer un objet lecteur via du code, vous pouvez procéder comme suit dans JavaScript :

```javascript
const avaPlayer = new ava.widgets.player();
```

Ou dans Typescript :

```typescript
const avaPlayer = new Player();
```

Ensuite, vous devez l’ajouter au code HTML :

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[API widget](https://github.com/Azure/video-analyzer/widgets)
