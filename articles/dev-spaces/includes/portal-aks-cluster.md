---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 6d2940712f8a76173de47badd45d7c7f0f0be05c
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825474"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Créer un cluster Kubernetes activé pour Azure Dev Spaces

1. Connectez-vous au portail Azure sur http://portal.azure.com.
1. Choisissez **Créer une ressource** > recherchez **Kubernetes** > sélectionnez **Kubernetes Service** > **Créer**.

   Suivez les étapes sous chaque en-tête du formulaire Créer un cluster AKS.

    - **DÉTAILS DU PROJET** : sélectionnez un abonnement Azure et un groupe de ressources Azure (nouveau ou existant).
    - **DÉTAILS DU CLUSTER** : entrez un nom une région (actuellement, vous devez choisir EastUS, WestEurope ou CanadaEast), une version et un préfixe de nom DNS pour le cluster AKS.
    - **ÉCHELLE** : sélectionnez une taille de machine virtuelle pour les nœuds de l’agent AKS et le nombre de nœuds. Si vous débutez avec Azure Dev Spaces, un nœud est suffisant pour explorer toutes les fonctionnalités. Une fois le cluster déployé, il est facile d’ajuster le nombre de nœuds. Notez que la taille de la machine virtuelle ne peut pas être changée une fois le cluster AKS créé. Toutefois, une fois qu’un cluster AKS a été déployé, vous pouvez facilement créer un nouveau cluster AKS avec des machines virtuelles plus volumineuses et utiliser Dev Spaces pour effectuer un nouveau déploiement sur ce cluster plus grand si vous avez besoin de monter en puissance.

   Prenez soin de choisir la version 1.9.6 de Kubernetes ou une version ultérieure.

   ![Paramètres de configuration Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Sélectionnez **Suivant : Mise en réseau** lorsque vous avez terminé.

1. Vérifiez que le routage d’applications HTTP est activé.

   ![Activer le routage d’applications HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Vous devez vous assurer que le routage des applications HTTP quand vous créez votre cluster AKS. Il n’est pas possible de changer ce paramètre ultérieurement.

1. Sélectionnez **Vérifier + créer**, puis **Créer** lorsque vous avez terminé.
