---
title: Créer une ressource de cluster Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource azure kubernetes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877442"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Créer une ressource de cluster Azure Kubernetes Service

1. Accédez au [Service Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks), puis sélectionnez **Créer**.

1. Sous l’onglet **De base**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Subscription|Sélectionner un abonnement approprié|
    |Resource group|Sélectionner un groupe de ressources disponible|
    |Nom du cluster Kubernetes|Entrer un nom (en minuscules)|
    |Région|Sélectionner un emplacement proche|
    |Version de Kubernetes|1.12.8 (par défaut)|
    |Préfixe du nom DNS|Créé automatiquement, mais vous pouvez le remplacer|
    |Taille du nœud|Standard DS2 v2 :<br>`2 vCPUs`, `7 GB`|
    |Nombre de nœuds|Conserver la valeur par défaut pour le curseur|

1. Sous l’onglet **Échelle**, conservez les valeurs par défaut pour les **nœuds virtuels** et les **groupes de machines virtuelles identiques (préversion)** définis sur les valeurs par défaut.
1. Sur l’onglet **Authentification**, conservez les valeurs par défaut définies pour **Principal du service** et **Activer RBAC**.
1. Sur l’onglet **Mise en réseau**, entrez les informations suivantes :

    |Paramètre|Valeur|
    |--|--|
    |Routage d’applications HTTP|Non|
    |Configuration de la mise en réseau|De base|

1. Sur l’onglet **Surveillance**, veillez à définir **Activer la surveillance du conteneur** sur **Oui** et conservez la valeur par défaut de **Espace de travail Log Analytics**.
1. Sur l’onglet **Balises**, laissez les paires nom/valeur vide pour l’instant.
1. Sélectionnez **Examiner et créer**.
1. Une fois la validation réussie, sélectionnez **Créer**.

> [!NOTE]
> Si la validation échoue, cela peut être dû à une erreur du principal du Service. Revenez à l’onglet **Authentification**, puis à **Vérifier + créer**, où la validation doit s’exécuter et réussir.
