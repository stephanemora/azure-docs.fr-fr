---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource azure kubernetes (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455105"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Créer une ressource de cluster Azure Kubernetes Service (AKS)

1. Accédez à [Créer](https://ms.portal.azure.com/#create/microsoft.aks) pour les services Kubernetes.

1. Sous l’onglet **De base**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Abonnement|Sélectionner l’abonnement approprié|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|
    |Nom du cluster Kubernetes|Nom de votre choix (en minuscules)|
    |Région|Sélectionner un emplacement proche|
    |Version de Kubernetes|1.12.8 (par défaut)|
    |Préfixe du nom DNS|Créé automatiquement, mais peut également remplacer|
    |Taille du nœud|Standard DS2 v2 :<br>`2 vCPUs`, `7 GB`|
    |Nombre de nœuds|Conserver la valeur par défaut pour le curseur|

1. Sur l’onglet **Échelle**, conservez les valeurs par défaut pour les *nœuds virtuels* et les *groupes de machines virtuelles identiques*.
1. Sur l’onglet **Authentification**, conservez les valeurs par défaut pour *Principal du service* et *Activer RBAC*.
1. Sur l’onglet **Mise en réseau**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Routage d’applications HTTP|Non|
    |Configuration de la mise en réseau|De base|

1. Sur l’onglet **Surveillance**, veillez à définir *Activer la surveillance du conteneur* sur **Oui** et conservez la valeur par défaut de *Espace de travail Log Analytics*
1. Sur l’onglet **Balises**, laissez les paires nom/valeur vide pour l’instant.
1. Cliquez sur **Examiner et créer**.
1. Une fois la validation réussie, cliquez sur **Créer**.

> [!NOTE]
> Si la validation échoue, cela peut être dû à une erreur du *principal du Service*. Revenez à l’onglet *Authentification*, puis à *Vérifier + créer*, où la validation doit s’exécuter et réussir.
