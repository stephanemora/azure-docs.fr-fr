---
title: Utiliser le widget du lecteur d’Azure Video Analyzer
description: Cet article de référence explique comment ajouter un widget du lecteur de Video Analyzer à votre application.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: ffc17e756a303723fe1d21c6ba221fed31147eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620570"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Utiliser le widget du lecteur d’Azure Video Analyzer

Ce tutoriel explique comment utiliser un widget du lecteur dans votre application. Ce code est un widget facile à incorporer, qui permet à vos utilisateurs de lire des vidéos et de naviguer dans les différentes parties d’un fichier vidéo segmenté. Pour le mettre en œuvre, vous allez générer une page HTML statique avec le widget incorporé et tous les éléments pour le faire fonctionner.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Créer un jeton
> * Répertorier les vidéos
> * Obtenir l’URL de base pour lire une [ressource d’application vidéo](./terminology.md#video)
> * Créer une page avec le lecteur
> * Passer un point de terminaison de streaming et un jeton au lecteur

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre ce tutoriel :

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur pour le fichier HTML.
* [Enregistrement et lecture de vidéo continue](./use-continuous-video-recording.md) ou [Détecter les mouvements et enregistrer des vidéos sur des appareils de périphérie](./detect-motion-record-video-clips-cloud.md)
* Créer un [jeton](./access-policies.md#creating-a-token)
* Créer une [stratégie d’accès](./access-policies.md#creating-an-access-policy)

## <a name="list-video-resources"></a>Lister les ressources vidéo

Ensuite, générez une liste des ressources vidéo. Vous effectuez un appel REST au point de terminaison de compte que vous avez utilisé précédemment et vous vous authentifiez avec le jeton que vous avez généré.

Il existe de nombreuses façons d’envoyer une requête GET à une API REST. En l’occurrence, vous allez utiliser une fonction JavaScript. Le code ci-dessous utilise une requête [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) couplée aux valeurs que vous stockez dans les champs `clientApiEndpointUrl` et `token` de la page pour envoyer une requête `GET` synchrone. Il prend ensuite la liste des vidéos obtenue et stocke celles-ci dans la zone de texte `videoList` que vous avez configurée sur la page.

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
   > [!NOTE]
   >Le `clientApiEndPoint` et le jeton sont ceux traités lors de la [création d’un jeton](./access-policies.md#creating-a-token).

## <a name="add-the-video-analyzer-player-component"></a>Ajouter le composant de lecteur de Video Analyzer

Vous disposez à présent d’une URL de point de terminaison d’API client, d’un jeton et d’un nom de vidéo. Vous pouvez ajouter le lecteur à la page.

1. Incluez le module lecteur proprement dit en ajoutant le package directement à votre page. Vous pouvez inclure la direction du package NPM dans votre application ou faire en sorte de l’incorporer de façon dynamique au moment de l’exécution comme ici :
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. Ajouter un élément `AVA-Player` au document :
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. Obtenez un lien vers le widget du lecteur de Video Analyzer qui se trouve dans la page :
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. Pour configurer le lecteur avec les valeurs dont vous disposez, vous devez configurer celles-ci en tant qu’objet comme ici :
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Chargez la vidéo dans le lecteur pour commencer.
   ```javascript
   avaPlayer.load();
   ```
   
## <a name="add-the-zone-drawer-component"></a>Ajouter le composant de dessinateur de zone

1. Ajoutez un élément AVA-Zone-Drawer au document :
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer"></ava-zone-drawer>
   ```
1. Obtenez un lien vers le dessinateur de zone de Video Analyzer qui se trouve dans la page :
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. Chargez le dessinateur de zone dans le lecteur :
   ```javascript
   zoneDrawer.load();
   ```
1. Pour créer et enregistrer des zones, vous devez ajouter des écouteurs d’événements ici :
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
   ```

## <a name="put-it-all-together"></a>Assemblage

En associant les éléments web précédents, vous obtenez la page HTML statique suivante. Cette page vous permet d’utiliser un point de terminaison de compte et un jeton pour voir une vidéo.

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
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="avaPlayer"></ava-player>
</ava-zone-drawer>
</body>
</html>
```

## <a name="host-the-page"></a>Héberger la page

Vous pouvez tester cette page localement, mais vous souhaiterez peut-être tester une version hébergée. Si vous n’avez pas de moyen rapide d’héberger une page, voici des instructions sur la façon de le faire en utilisant des [sites web statiques](../../storage/blobs/storage-blob-static-website.md) avec Stockage Azure. La procédure qui suit est une version condensée de ces [instructions plus complètes](../../storage/blobs/storage-blob-static-website-how-to.md). Ces étapes sont adaptées aux fichiers que vous utilisez dans ce tutoriel.

1. Créez un compte de stockage.
1. Sous **Gestion des données**, sélectionnez **Site web statique**.
1. Activez le site web statique sur le compte de stockage.
1. Pour **Nom du document d’index**, entrez **index.html**.
1. Pour **Chemin du document d’erreur**, entrez **404.html**.
1. Sélectionnez **Enregistrer**.
1. Notez le **point de terminaison principal** qui s’affiche. Il s’agira de votre site web.
1. Au-dessus de **Point de terminaison principal**, sélectionnez **$web**.
1. À l’aide du bouton **Charger** situé en haut, chargez votre page HTML statique en tant que **index.html**.

## <a name="play-a-video"></a>Lire une vidéo

La page est à présent hébergée. Accédez-y et suivez les étapes pour lire une vidéo.

1. Renseignez les valeurs **Client API endpoint URL** (URL de point de terminaison d’API client) et **Token**.
1. Sélectionnez **Get videos**.
1. Dans la liste de vidéos, sélectionnez un nom de vidéo et entrez-le dans le champ **Video name**.
1. Sélectionnez **Play video**.

## <a name="additional-details"></a>Informations supplémentaires

Les sections suivantes contiennent des informations supplémentaires importantes, que vous devez connaître.

### <a name="refresh-the-access-token"></a>Actualiser le jeton d’accès

Le lecteur utilise le jeton d’accès que vous avez généré précédemment pour obtenir un jeton d’autorisation de lecture. Les jetons expirent périodiquement et doivent être actualisés. Il existe deux manières d’actualiser le jeton d’accès pour le lecteur après en avoir généré un :

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

L’événement `TOKEN_EXPIRED` se produit 5 secondes avant l’expiration du jeton. Si vous définissez un détecteur d’événements, vous devez le faire avant d’appeler la fonction `load` sur le widget du lecteur.

### <a name="configuration-details"></a>Détails de configuration

La configuration du lecteur ci-dessus est simple, mais vous pouvez utiliser un éventail d’options plus large pour les valeurs de configuration. Les champs pris en charge sont les suivants :

| Name   | Type             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | string | Votre jeton JWT pour le widget |
| `videoName` | string | Nom de la ressource vidéo  |
| `clientApiEndpointUrl` | string | URL de point de terminaison pour l’API client |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>Autres façons de charger le code dans votre application

Le package utilisé pour récupérer le code dans votre application est un [package NPM](https://www.npmjs.com/package/@azure/video-analyzer-widgets). Dans l’exemple précédent, la version la plus récente a été chargée au moment de l’exécution directement à partir du référentiel. Toutefois, vous pouvez également télécharger et installer le package localement à l’aide de la commande suivante :

```bash
npm install @azure/video-analyzer/widgets
```

Vous pouvez aussi l’importer dans le code de votre application en utilisant le code suivant pour TypeScript :

```typescript
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

Si vous souhaitez créer un widget de lecteur dynamiquement, vous pouvez utiliser le code suivant pour JavaScript :
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


Si vous utilisez cette méthode pour importer, vous devrez créer les objets dessinateur de zone et lecteur par programmation une fois l’importation terminée.  Dans l’exemple précédent, vous avez ajouté le module à la page à l’aide de la balise HTML `ava-player`. Pour créer un objet dessinateur de zone et un objet lecteur par le biais du code, vous pouvez procéder comme suit dans JavaScript :


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

Ou dans TypeScript :

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

Ensuite, vous devez l’ajouter au code HTML :

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur l’[API widget](https://github.com/Azure/video-analyzer/tree/main/widgets).
