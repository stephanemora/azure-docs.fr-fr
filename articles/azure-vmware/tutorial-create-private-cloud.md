---
title: 'Tutoriel : Déployer un cloud privé Azure VMware Solution'
description: Découvrez comment créer et déployer un cloud privé Azure VMware Solution
ms.topic: tutorial
ms.date: 06/11/2021
ms.openlocfilehash: d91e9fe9261aa4a04f5e5dffd3505742d9886623
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730353"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Déployer un cloud privé Azure VMware Solution

Un cloud privé Azure VMware Solution vous donne la possibilité de déployer un cluster vSphere sur Azure. Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster est de trois. Des hôtes supplémentaires peuvent être ajoutés un par un, jusqu’à un maximum de 16 hôtes par cluster. Le nombre maximal de clusters par cloud privé est de quatre.  Le déploiement initial de VMware Azure Solution comporte trois hôtes. 

Des clusters d’essai sont disponibles à des fins d’évaluation et limités à trois hôtes. Il existe un cluster d’évaluation unique par cloud privé. Vous pouvez mettre à l’échelle un cluster d’évaluation à l’aide d’un seul hôte au cours de la période d’évaluation.

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.

Comme Azure VMware Solution ne vous permet pas de gérer votre cloud privé avec votre vCenter local au lancement, vous devrez effectuer une configuration supplémentaire.  Ce tutoriel décrit ces étapes et les conditions préalables associées.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un cloud privé Azure VMware Solution
> * Vérifier le cloud privé déployé

## <a name="prerequisites"></a>Prérequis

- Droits d’administration appropriés et autorisation de créer un cloud privé. Vous devez avoir un niveau de contributeur minimal dans l’abonnement.
- Suivez les informations que vous avez rassemblées dans le tutoriel sur la [planification](plan-private-cloud-deployment.md) pour déployer Azure VMware Solution.
- Vérifiez que vous disposez du réseau configuré approprié comme décrit dans la [liste de vérification de la planification réseau](tutorial-network-checklist.md).
- Les hôtes ont été approvisionnés et le [fournisseur de ressources Microsoft.AVS a été inscrit](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider).

## <a name="create-a-private-cloud"></a>Créer un cloud privé

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un cloud privé Azure VMware Solution
> * Vérifier le cloud privé déployé
> * Supprimer un cloud privé Azure VMware Solution

Passez au didacticiel suivant pour découvrir comment créer un serveur de rebond. Vous utilisez le serveur de rebond pour vous connecter à votre environnement afin de pouvoir gérer votre cloud privé en local.


> [!div class="nextstepaction"]
> [Accéder à un cloud privé Azure VMware Solution](tutorial-access-private-cloud.md)
