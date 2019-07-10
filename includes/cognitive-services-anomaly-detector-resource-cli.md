---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503632"
---
Les commandes Azure CLI suivantes configurent une ressource de Détecteur d'anomalies sur le niveau tarifaire gratuit. Cliquez sur le bouton **Essayer**, collez le code et appuyez sur Entrée pour l’exécuter dans le shell Azure Cloud. Cela affichera vos clés pour authentifier votre application. Une fois cela terminé, [créez une variable d’environnement](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour une de vos clés, et nommez-la `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Vous pouvez éventuellement :
>    * créer une ressource en utilisant le [portail Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) ou [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) sur votre ordinateur local.
>    * obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * afficher cette ressource sur le [portail Azure](https://portal.azure.com/). 

Les Cognitive Services sont représentés par des ressources Azure que vous devez configurer. Chaque compte Cognitive Services (et ses ressources Azure associées) doivent appartenir à un groupe de ressources Azure.

1. Création d’un groupe de ressources Azure

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Créer une ressource Détecteur d’anomalies sur le niveau gratuit

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Répertorier les clés pour votre ressource

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```