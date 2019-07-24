---
title: Bien démarrer avec le contrôle de carte web dans Azure Maps | Microsoft Docs
description: Découvrez comment utiliser la bibliothèque JavaScript côté client Azure Maps Map Control.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: aa923fa7e2d5e673e6a2db2b349e54d433d1817b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957276"
---
# <a name="use-the-azure-maps-map-control"></a>Utiliser le Map Control Azure Maps

La bibliothèque Javascript côté client Map Control vous permet d’effectuer le rendu de cartes et des fonctionnalités Azure Maps intégrées dans votre application web ou mobile.

## <a name="create-a-new-map-in-a-web-page"></a>Créer une carte dans une page web

Vous pouvez intégrer une carte dans une page web à l’aide de la bibliothèque JavaScript côté client Map Control.

1. Créez un fichier HTML.

2. Chargez-le dans le SDK web Azure Maps. Cela est possible à l’aide de l’une des deux options suivantes.

    a. Utilisez la version CDN hébergée globalement du SDK Web d’Azure Maps en ajoutant les points de terminaison d’URL à la feuille de style et aux références de script dans l’élément `<head>` du fichier :

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Vous pouvez également charger le code source du SDK Web d’Azure Maps localement en utilisant le package NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) et hébergez-le avec votre application. Ce package inclut aussi des définitions de TypeScript.

    > npm install azure-maps-control

    Ajoutez ensuite les références à la feuille de styles Azure Maps et les références de la source du script à l’élément `<head>` du fichier :

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

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

4. Dans le corps de la page, ajoutez un élément `<div>` et attribuez-lui l’`id` **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Pour initialiser le contrôle de carte, définissez une nouvelle section dans le corps HTML et créez un script. Transmettez `id` dans la carte `<div>` ou un `HTMLElement` (par exemple, `document.getElementById('myMap')`) comme premier paramètre lors de la création d’une instance de classe `Map`. Utilisez votre propre clé de compte Azure Maps ou vos identifiants Azure Active Directory (AAD) pour authentifier la carte à l’aide des [options d’authentification](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Si vous devez créer un compte ou rechercher votre clé, consultez la section [Guide de gestion de votre compte et de vos clés Azure Maps](how-to-manage-account-keys.md). L’option **language** spécifie la langue à utiliser pour les étiquettes de carte et les contrôles. Pour plus d’informations sur les langues prises en charge, consultez [Langues prises en charge](supported-languages.md). En cas d’utilisation d’une clé d’abonnement pour l’authentification.

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

    En cas d’utilisation d’Azure Active Directory (AAD) pour l’authentification :

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
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Pour plus d’informations, consultez [Authentification avec Azure Maps](azure-maps-authentication.md).

6. Si vous le souhaitez, vous pouvez ajouter les éléments de balises META suivants à l’en-tête de votre page :

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Dans l’ensemble, votre fichier HTML devrait ressembler au code suivant :

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

8. Ouvrez le fichier dans votre navigateur web et consultez la carte ayant fait l’objet du rendu. Elle doit ressembler au code suivant :

    <iframe height="700" style="width: 100%;" scrolling="no" title="Comment utiliser le contrôle de carte" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Consultez la section <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>Comment utiliser le contrôle de carte</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une carte et interagir avec elle :

> [!div class="nextstepaction"]
> [Créer une carte](map-create.md)

Découvrez comment appliquer un style à un mappage :

> [!div class="nextstepaction"]
> [Choisir un style de carte](choose-map-style.md)
