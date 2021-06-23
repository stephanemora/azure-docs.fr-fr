---
title: Régions dans lesquelles Azure Video Analyzer for Media (anciennement Video Indexer) est disponible - Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: Cet article présente les régions Azure dans lesquelles Azure Video Analyzer for Media (anciennement Video Indexer) est disponible.
services: media-services
author: Juliako
manager: femila
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 900a8b1b8fdc35997e0ad7ee09430f4eb33a4d9e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385975"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>Régions Azure dans lesquelles Video Analyzer for Media est disponible

Les API Azure Video Analyzer for Media (anciennement Video Indexer) contiennent un paramètre **location** que vous devez définir sur la région Azure vers laquelle l’appel doit être routé. Il doit s’agir d’une [région Azure dans laquelle Video Analyzer for Media est disponible](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Emplacements

Le paramètre `location` doit avoir pour valeur le nom de code de la région Azure. Si vous utilisez Video Analyzer for Media en mode préversion, vous devez définir la valeur `"trial"`. `trial` est la valeur par défaut pour le paramètre `location`. Sinon, pour obtenir le nom de code de la région Azure dans laquelle se trouve votre compte et vers laquelle votre appel doit être routé, vous pouvez utiliser le portail Azure ou exécuter une commande [Azure CLI](/cli/azure).

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au site web [Azure Video Analyzer for Media](https://www.videoindexer.ai/).
1. Sélectionnez **Comptes d’utilisateurs** en haut à droite de la page.
1. Recherchez l’emplacement de votre compte en haut à droite.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Lieu":::
    
###  <a name="cli-command"></a>Commande CLI

```azurecli-interactive
az account list-locations
```

Une fois que vous avez exécuté la ligne ci-dessus, vous obtenez une liste de toutes les régions Azure. Accédez à la région Azure qui présente la valeur *displayName* que vous recherchez et utilisez sa valeur *name* pour le paramètre **location**.

Par exemple, pour la région Azure USA Ouest 2 affichée ci-dessous, vous allez utiliser « westus2 » pour le paramètre **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Étapes suivantes

- [Personnaliser le modèle de langue à l’aide des API](customize-language-model-with-api.md)
- [Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)
- [Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
