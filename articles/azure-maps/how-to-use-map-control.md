---
title: Bien démarrer avec le contrôle de carte web | Microsoft Azure Maps
description: Découvrez comment utiliser la bibliothèque Javascript côté client du contrôle de carte Microsoft Azure Maps pour effectuer le rendu de cartes et de fonctionnalités Azure Maps intégrées dans votre application web ou mobile.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 90cd752ddd46999627de538abe3ca6b1926005aa
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264121"
---
# <a name="use-the-azure-maps-map-control"></a>Utiliser le contrôle de carte Azure Maps

La bibliothèque Javascript côté client Map Control vous permet d’effectuer le rendu de cartes et des fonctionnalités Azure Maps intégrées dans votre application web ou mobile.

## <a name="create-a-new-map-in-a-web-page"></a>Créer une carte dans une page web

Vous pouvez intégrer une carte dans une page web à l’aide de la bibliothèque JavaScript côté client Map Control.

1. Créez un fichier HTML.

2. Chargez-le dans le SDK web Azure Maps. Vous pouvez choisir l'une des deux options suivantes :

a. Utilisez la version CDN hébergée globalement du SDK Web d’Azure Maps en ajoutant les points de terminaison d’URL à la feuille de style et aux références de script dans l’élément `<head>` du fichier :

```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
```

b. Chargez le code source du SDK Web d'Azure Maps localement en utilisant le package NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) et hébergez-le avec votre application. Ce package inclut aussi des définitions de TypeScript.

> **npm install azure-maps-control**

Ajoutez ensuite les références à la feuille de styles Azure Maps et les références de la source du script à l’élément `<head>` du fichier :

```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
```

    >[!Note]
    > Typescript definitions can be imported into your application by adding the following code:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Pour afficher la carte de sorte qu’elle occupe le corps de la page en totalité, ajoutez l’élément `<style>` suivant à l’élément `<head>`.

```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
```

4. Dans le corps de la page, ajoutez un élément `<div>` et attribuez-lui l’`id`**myMap**.

```HTML
    <body>
        <div id="myMap"></div>
    </body>
```

5. Pour initialiser le contrôle de carte, définissez une nouvelle balise de script dans le corps HTML. Transmettez `id` dans la carte `<div>` ou un `HTMLElement` (par exemple, `document.getElementById('myMap')`) comme premier paramètre lors de la création d’une instance de classe `Map`. Utilisez votre propre clé de compte Azure Maps ou vos identifiants Azure Active Directory (AAD) pour authentifier la carte à l’aide des [options d’authentification](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

Si vous avez besoin de créer un compte ou de rechercher votre clé, suivez les instructions des sections [Créer un compte](quick-demo-map-app.md#create-an-account-with-azure-maps) et [Obtenir une clé primaire](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

L’option **language** spécifie la langue à utiliser pour les étiquettes de carte et les contrôles. Pour plus d'informations sur les langues prises en charge, consultez [Langues prises en charge](supported-languages.md). Si vous utilisez une clé d'abonnement pour l'authentification, utilisez ce qui suit :

```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
 ```

Si vous utilisez Azure Active Directory (AAD) pour l'authentification, utilisez ce qui suit :

```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
```

Vous trouverez une liste d’exemples montrant comment intégrer AAD (Azure Active Directory) à Azure Maps [ici](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
Pour plus d’informations, consultez le document [Authentification avec Azure Maps](azure-maps-authentication.md) ainsi que les [exemples d’authentification d’Azure AD auprès d’Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Si vous le souhaitez, vous pouvez ajouter les éléments de balises META suivants à l'en-tête de votre page :

```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

7. Dans l'ensemble, votre fichier HTML devrait ressembler au code suivant :

```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
 ```

8. Ouvrez le fichier dans votre navigateur web et consultez la carte ayant fait l’objet du rendu. Elle doit se présenter comme l'image ci-dessous :

![Image de la carte présentant le résultat rendu](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Localisation de la carte

Azure Maps propose deux méthodes pour définir la langue et l'affichage régional de la carte rendue. La première option consiste à ajouter ces informations à l’espace de noms `atlas` global, ce qui a pour effet que toutes les instances de contrôle de carte dans votre application ont ces paramètres par défaut. Le code suivant définit la langue sur le français (« fr-FR ») et l’affichage régional sur « Auto » :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

La deuxième option consiste à transmettre ces informations dans les options lors du chargement de la carte, comme suit :

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Le Kit de développement logiciel (SDK) web permet de charger plusieurs instances de carte sur la même page avec des paramètres de langue et de région différents. De plus, ces paramètres peuvent être mis à jour une fois la carte chargée à l'aide de la fonction `setStyle` de la carte. 

Voici un exemple Azure Maps dans lequel la langue est définie sur « fr-FR » et l’affichage régional sur « Auto ».

![Image de carte affichant des étiquettes en français](./media/how-to-use-map-control/websdk-localization.png)

La liste complète des langues et des affichages régionaux pris en charge est disponible [ici](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Prise en charge du cloud Azure Government

Le SDK web d’Azure Maps prend en charge le cloud Azure Government. Toutes les URL JavaScript et CSS utilisées pour accéder au SDK web d'Azure Maps restent les mêmes. Les tâches suivantes devront être effectuées pour se connecter à la version cloud d'Azure Government de la plateforme Azure Maps.

Quand vous utilisez le contrôle de carte interactif, ajoutez la ligne de code suivante avant de créer une instance de la classe `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Veillez à utiliser les détails d'authentification Azure Maps à partir de la plateforme du cloud Azure Government lors de l'authentification de la carte et des services.

Quand vous utilisez le module de services, le domaine des services doit être défini lors de la création d’une instance d’un point de terminaison d’URL d’API. Par exemple, le code suivant crée une instance de la classe `SearchURL` et pointe le domaine vers le cloud Azure Government.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Si vous accédez directement aux services REST d’Azure Maps, définissez le domaine de l’URL sur `atlas.azure.us`. Par exemple, si vous utilisez le service API de recherche, remplacez le domaine URL `https://atlas.microsoft.com/search/` par `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une carte et interagir avec elle :

> [!div class="nextstepaction"]
> [Créer une carte](map-create.md)

Découvrez comment appliquer un style à un mappage :

> [!div class="nextstepaction"]
> [Choisir un style de carte](choose-map-style.md)

Pour ajouter des données à votre carte :

> [!div class="nextstepaction"]
> [Créer une carte](map-create.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Pour obtenir une liste d’exemples montrant comment intégrer AAD (Azure Active Directory) à Azure Maps, consultez :

> [!div class="nextstepaction"]
> [Exemples d’authentification Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)