---
title: Utiliser le widget du lecteur d’Azure Video Analyzer
description: Cet article de référence explique comment ajouter un widget du lecteur de Video Analyzer à votre application.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: b70bfc9a10e357c6f1e64c1737fdb4c049b505f5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037441"
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
* Avoir suivi le tutoriel [Enregistrement et lecture de vidéo continue](./use-continuous-video-recording.md) ou le guide de démarrage rapide [Détecter les mouvements et enregistrer des vidéos sur des appareils de périphérie](./detect-motion-record-video-clips-cloud.md).

En outre, il est utile de connaître les ressources suivantes :

- [Composants web](https://developer.mozilla.org/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="create-a-token"></a>Créer un jeton

Dans cette section, vous allez créer un jeton JWT (JSON Web Token) que vous utiliserez plus loin dans cet article. Vous allez utiliser un exemple d’application qui va générer le jeton JWT et vous fournir tous les champs nécessaires pour créer la stratégie d’accès.

> [!NOTE] 
> Si vous savez comment générer un jeton JWT basé sur un certificat RSA ou ECC, vous pouvez ignorer cette section.

1. Clonez le [dépôt des exemples C# AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). Ensuite, accédez au dossier *src/jwt-token-issuer*, puis recherchez l’application *JWTTokenIssuer*.

    > [!NOTE] 
    > Pour plus d’informations sur la configuration de vos valeurs d’audience, consultez [Stratégies d’accès](./access-policies.md).

2. Ouvrez Visual Studio Code, puis accédez au dossier dans lequel vous avez téléchargé l’application *JWTTokenIssuer*. Ce dossier doit contenir le fichier *\*.csproj*.
3. Dans le volet de l’explorateur, accédez au fichier *program.cs*.
4. À la ligne 77, remplacez l’audience par le point de terminaison d’Azure Video Analyzer, suivi de /videos/\*. Il doit se présenter comme suit :

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Vous pouvez trouver le point de terminaison de Video Analyzer dans la section de vue d’ensemble de la ressource Video Analyzer dans le portail Azure. La section [Lister les ressources vidéo](#list-video-resources), plus loin dans cet article, fait référence à cette valeur sous la forme `clientApiEndpointUrl`.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Capture d’écran montrant le point de terminaison du widget du lecteur.":::
    
5. À la ligne 78, remplacez l’émetteur par la valeur de l’émetteur de votre certificat (par exemple, `https://contoso.com`).
6. Enregistrez le fichier . Vous verrez peut-être le message **« jwt token issuer » ne contient pas les ressources nécessaires à la génération et au débogage. Voulez-vous les ajouter ?** Sélectionnez **Oui**.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Capture d’écran montrant l’invite relative aux ressources nécessaires dans Visual Studio Code.":::
   
7. Ouvrez la fenêtre d’invite de commandes et accédez au dossier contenant les fichiers *JWTTokenIssuer*. Exécutez les deux commandes suivantes : `dotnet build`, suivie de `dotnet run`. Si vous avez l’extension C# sur Visual Studio Code, vous pouvez également appuyer sur F5 pour exécuter l’application *JWTTokenIssuer*.

L'application se génère et s'exécute. Une fois générée, elle crée un certificat auto-signé et génère les informations de jeton JWT à partir de ce certificat. Vous pouvez également exécuter le fichier *JWTTokenIssuer.exe* qui se trouve dans le dossier de débogage du répertoire à partir duquel *JWTTokenIssuer* a été généré. L’avantage de l’exécution de l’application est que vous pouvez spécifier des options d’entrée comme suit :

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

*JWTTokenIssuer* crée le jeton JWT et les composants nécessaires suivants :

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

**Veillez à copier et enregistrer ces valeurs pour une utilisation ultérieure.**

## <a name="create-an-access-policy"></a>Définition d’une stratégie d’accès.

Des stratégies d’accès définissent les autorisations et la durée d’accès à un flux vidéo spécifique. Pour les besoins de ce tutoriel, configurez une stratégie d’accès pour Video Analyzer dans le portail Azure.  

1. Connectez-vous au portail Azure et accédez au groupe de ressources où se trouve votre compte Video Analyzer.
1. Sélectionnez la ressource Video Analyzer.
1. Sous **Video Analyzer**, sélectionnez **Stratégies d’accès**.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Capture d’écran montrant l’option Stratégies d’accès.":::
   
1. Sélectionnez **Nouveau** et entrez les informations suivantes :

   - **Nom de la stratégie d’accès** : vous pouvez choisir n’importe quel nom.

   - **Émetteur** : cette valeur doit correspondre à l’émetteur du jeton JWT. 

   - **Audience** : l’audience pour ce jeton JWT. `${System.Runtime.BaseResourceUrlPattern}` est la valeur par défaut. Pour en savoir plus sur l’audience et sur `${System.Runtime.BaseResourceUrlPattern}`, consultez [Stratégies d’accès](./access-policies.md).

   - **Type de clé** : RSA 

   - **Algorithme** : les valeurs prises en charge sont RS256, RS384 et RS512.

   - **ID de clé** : cet ID est généré à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#create-a-token).

   - **Module clé RSA** : cette valeur est générée à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#create-a-token).

   - **Exposant clé RSA** : cette valeur est générée à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#create-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Capture d’écran montrant le portail des stratégies d’accès."::: 
   
   > [!NOTE] 
   > Ces valeurs proviennent de l’application *JWTTokenIssuer* créée à l’étape précédente.

1. Sélectionnez **Enregistrer**.

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
   >Le `clientApiEndPoint` et le jeton sont ceux traités dans la section [Créer un jeton](#create-a-token).

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
import { Player } from '@video-analyzer/widgets';
```

Si vous souhaitez créer un widget de lecteur dynamiquement, vous pouvez utiliser le code suivant pour JavaScript :
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

Si vous utilisez cette méthode pour importer, vous devrez créer l’objet lecteur par programmation une fois l’importation terminée. Dans l’exemple précédent, vous avez ajouté le module à la page à l’aide de la balise HTML `ava-player`. Pour créer un objet lecteur avec du code, vous pouvez procéder comme suit dans JavaScript :

```javascript
const avaPlayer = new ava.widgets.player();
```

Ou dans TypeScript :

```typescript
const avaPlayer = new Player();
```

Ensuite, vous devez l’ajouter au code HTML :

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur l’[API widget](https://github.com/Azure/video-analyzer/tree/main/widgets).
