---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource d’instance de conteneur Azure (ACI).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455071"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Créer une ressource d’instance de conteneur Azure (ACI)

1. Accédez à la page [Créer](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) de Container Instances.

2. Sous l’onglet **De base**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Abonnement|Sélectionnez votre abonnement.|
    |Groupe de ressources|Sélectionnez le groupe de ressources disponible ou créez-en un tel que `cognitive-services`.|
    |Nom du conteneur|Entrez un nom tel que `cognitive-container-instance`. Ce nom doit être en minuscules.|
    |Location|Sélectionnez une région pour le déploiement.|
    |Type d’image|`Public`|
    |Nom de l’image|Entrez l’emplacement du conteneur Cognitive Services. Il peut s’agir du même emplacement que celui que vous avez utilisé dans la commande `docker pull`, _par exemple_ : <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Type de système d’exploitation|`Linux`|
    |Size|Appliquez les suggestions de taille pour votre conteneur Cognitive Services :<br>2 cœurs<br>4 Go

3. Sur l’onglet **Mise en réseau**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Ports|Définissez le port TCP sur `5000`. Expose le conteneur sur le port 5000.|

4. Sur l’onglet **Avancé** , entrez les informations suivantes pour transmettre les [paramètres de facturation requis](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) du conteneur à la ressource ACI :

    |Clé de la page Avancé|Valeur de la page Avancé|
    |--|--|
    |`apikey`|Copiée de la page **Clés** de la ressource Analyse de texte. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiée de la page **Vue d’ensemble** de la ressource Analyse de texte. Exemple : `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Cliquez sur **Examiner et créer**
1. Une fois la validation réussie, cliquez sur **Créer** pour terminer le processus de création
1. Lorsque la ressource est déployée avec succès, elle est prête à être utilisée