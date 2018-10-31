---
title: Guide pratique d’utilisation de la bibliothèque Azure Maps Map Control | Microsoft Docs
description: Découvrez comment utiliser la bibliothèque JavaScript côté client Azure Maps Map Control.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166019"
---
# <a name="use-the-azure-maps-map-control"></a>Utiliser le Map Control Azure Maps

La bibliothèque Javascript côté client Map Control vous permet d’effectuer le rendu de cartes et des fonctionnalités Azure Maps intégrées dans votre application web ou mobile.

## <a name="create-a-new-map-in-a-web-page"></a>Créer une carte dans une page web

Vous pouvez intégrer une carte dans une page web à l’aide de la bibliothèque JavaScript côté client Map Control.

1. Créez un fichier et nommez-le **MapSearch.html**.

2. Ajoutez la feuille de styles Azure Maps et les références de la source du script à l’élément `<head>` du fichier :

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Afin d’effectuer le rendu d’un nouveau mappage dans votre navigateur, ajoutez une référence **#map** dans l’élément `<style>` :

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Pour initialiser le contrôle de carte, définissez une nouvelle section dans le corps HTML et créez un script. Utilisez votre propre clé de compte Azure Maps dans le script. Si vous devez créer un compte ou rechercher votre clé, consultez la section [Guide de gestion de votre compte et de vos clés Azure Maps](how-to-manage-account-keys.md). La méthode **setLanguage** spécifie la langue à utiliser pour les étiquettes de carte et les contrôles. Pour plus d’informations sur les langues prises en charge, consultez [Langues prises en charge](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Ouvrez le fichier dans votre navigateur web et consultez la carte ayant fait l’objet du rendu.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer un mappage à l’aide d’un exemple complet :

> [!div class="nextstepaction"]
> [Créer une carte](map-create.md)

Découvrez comment appliquer un style à un mappage :

> [!div class="nextstepaction"]
> [Choisir un style de carte](choose-map-style.md)
