---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: feb79d047a6c3b25176a13dcc3c3afd53a51459e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444901"
---
## <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a>.
1. Sélectionnez la ressource <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">Créer un détecteur d’anomalies</a>.
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Value|
    |--|--|
    |Name|Nom de votre choix (2-64 caractères)|
    |Subscription|Sélectionner l’abonnement approprié|
    |Location|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - 10 appels par seconde, 20 000 transactions par mois. <br> Ou :<br> `S0` - 80 appels par seconde|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources
1. Collectez le `endpoint` configuré et une clé d’API :

    |Onglet Clés et points de terminaison dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Clés**|Clé de l’API|Copiez 1 des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



