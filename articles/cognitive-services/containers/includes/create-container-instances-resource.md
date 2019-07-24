---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource d’instance de conteneur Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717156"
---
## <a name="create-an-azure-container-instance-resource"></a>Créer une ressource d’instance de conteneur Azure

1. Accédez à la page [Créer](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) de Container Instances.

2. Sous l’onglet **De base**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Subscription|Sélectionnez votre abonnement.|
    |Resource group|Sélectionnez le groupe de ressources disponible ou créez-en un tel que `cognitive-services`.|
    |Nom du conteneur|Entrez un nom tel que `cognitive-container-instance`. Ce nom doit être en minuscules.|
    |Location|Sélectionnez une région pour le déploiement.|
    |Type d’image|`Public`|
    |Nom de l’image|Entrez l’emplacement du conteneur Cognitive Services. L’emplacement peut être identique à celui utilisé dans la commande `docker pull` ; reportez-vous à [Référentiels et images de conteneur](../../cognitive-services-container-support.md#container-repositories-and-images) pour les noms d’image disponibles et leur référentiel correspondant.|
    |Type de système d’exploitation|`Linux`|
    |Size|Appliquez les suggestions de taille pour votre conteneur Cognitive Services :<br>2 cœurs de processeur<br>4 Go

3. Sur l’onglet **Mise en réseau**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Ports|Définissez le port TCP sur `5000`. Expose le conteneur sur le port 5000.|

4. Dans l’onglet **Avancé** , entrez les **Variables d’environnement** requises pour les [paramètres de facturation](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) du conteneur de la ressource ACI :

    | Clé | Valeur |
    |--|--|
    |`apikey`|Copiée de la page **Clés** de la ressource Analyse de texte. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiée de la page **Vue d’ensemble** de la ressource Analyse de texte. Exemple : `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Cliquez sur **Examiner et créer**
1. Une fois la validation réussie, cliquez sur **Créer** pour terminer le processus de création
1. Lorsque la ressource est déployée avec succès, elle est prête