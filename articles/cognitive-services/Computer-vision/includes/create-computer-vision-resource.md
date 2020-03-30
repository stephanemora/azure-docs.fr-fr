---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499106"
---
## <a name="create-an-computer-vision-resource"></a>Créer une ressource Vision par ordinateur

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur la ressource [Créer **Vision par ordinateur**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Name|Nom de votre choix (2-64 caractères)|
    |Subscription|Sélectionner l’abonnement approprié|
    |Location|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - le niveau tarifaire minimal|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources.
1. Collectez les `{ENDPOINT_URI}` et `{API_KEY}` configurés, consultez [collecte des paramètres requis](../computer-vision-how-to-install-containers.md#gathering-required-parameters) pour obtenir plus d’informations.
