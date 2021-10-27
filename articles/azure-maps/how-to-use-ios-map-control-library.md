---
title: Bien démarrer avec le contrôle de carte iOS | Microsoft Azure Maps
description: Familiarisez-vous avec le kit SDK iOS Azure Maps. Consultez la section expliquant comment installer le kit SDK et créer une carte interactive.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 53d5b70f62d3ccea3d881a4e91a4b16287e1aa49
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043336"
---
# <a name="getting-started-with-azure-maps-ios-sdk-public-preview"></a>Bien démarrer avec le kit SDK iOS Azure Maps (préversion publique)

Le kit SDK iOS Azure Maps est une bibliothèque de cartes vectorielles pour iOS. Cet article vous guide tout au long des processus d’installation du kit SDK iOS Azure Maps et de chargement d’une carte.

## <a name="prerequisites"></a>Prérequis

Veillez à suivre la procédure de l’article [Démarrage rapide : Créer une application iOS](quick-ios-app.md).

## <a name="localizing-the-map"></a>Localisation de la carte

Le kit SDK iOS Azure Maps propose trois méthodes pour définir la langue et l’affichage régional de la carte. Le code suivant illustre les différentes manières de définir la *langue* sur le français (« fr-FR ») et *l’affichage régional* sur « Auto ».

1. Transmettre les informations sur la langue et l’affichage régional à la classe `AzureMaps` en utilisant les propriétés statiques `language` et `view`. Cette opération définit les propriétés de langue et d’affichage régional par défaut dans votre application.

    ```swift
    // Alternatively use Azure Active Directory authenticate.
    AzureMaps.configure(aadClient: "<Your aad clientId>", aadAppId: "<Your aad AppId>", aadTenant: "<Your aad Tenant>")
    
    // Set your Azure Maps Key.
    // AzureMaps.configure(subscriptionKey: "<Your Azure Maps Key>")
    
    // Set the language to be used by Azure Maps.
    AzureMaps.language = "fr-FR"
    
    // Set the regional view to be used by Azure Maps.
    AzureMaps.view = "Auto"
    ```

1. Vous pouvez également transmettre les informations sur la langue et l’affichage régional au init du contrôle de carte.

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. La dernière manière de définir programmatiquement les propriétés de langue et de vue régionale utilise la méthode Maps `setStyle`. Effectuez cette opération à chaque fois que vous devez changer la langue et l’affichage régional de la carte.

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

Voici un exemple d’application Azure Maps dans laquelle la langue est définie sur « fr-FR » et l’affichage régional sur « Auto ».

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="Image de carte affichant des étiquettes en français.":::

Pour obtenir la liste complète des langues et des affichages régionaux pris en charge, consultez [Prise en charge de la localisation dans Azure Maps](supported-languages.md).

## <a name="navigating-the-map"></a>Navigation dans la carte

Cette section détaille les différentes façons de naviguer dans un programme Azure Maps.

### <a name="zoom-the-map"></a>Faire un zoom sur la carte

* Touchez la carte avec deux doigts et rapprochez-les pour faire un zoom arrière ou écartez-les pour faire un zoom avant.
* Appuyez deux fois sur la carte pour faire un zoom avant d’un niveau.
* Appuyez deux fois avec deux doigts pour faire un zoom arrière d’un niveau de la carte.
* Appuyez deux fois ; sur le second appui, maintenez votre doigt sur la carte et faites glisser vers le haut pour effectuer un zoom avant, ou vers le bas pour effectuer un zoom arrière.

### <a name="pan-the-map"></a>Effectuer un panoramique sur la carte

* Touchez la carte et faites-la glisser dans n’importe quelle direction.

### <a name="rotate-the-map"></a>Faire pivoter la carte

* Touchez la carte avec deux doigts et effectuez une rotation.

### <a name="pitch-the-map"></a>Incliner la carte

* Touchez la carte avec deux doigts et faites-les glisser ensemble vers le haut ou vers le bas.

## <a name="azure-government-cloud-support"></a>Prise en charge du cloud Azure Government

Le kit SDK iOS Azure Maps prend en charge le cloud Azure Government. Vous spécifiez l’utilisation du domaine cloud Government Azure Maps en ajoutant la ligne de code suivante qui contient les détails d’authentification auprès d’Azure Maps :

```
AzureMaps.domain = "atlas.azure.us"
```

Veillez à utiliser les détails d'authentification Azure Maps à partir de la plateforme du cloud Azure Government lors de l'authentification de la carte et des services.