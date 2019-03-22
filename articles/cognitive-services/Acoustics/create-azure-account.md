---
title: Configuration du compte Azure Batch projet acoustique
titlesuffix: Azure Cognitive Services
description: Cette procédure décrit comment configurer un compte Azure Batch pour une utilisation avec des intégrations de moteur de projet acoustique Unity et Unreal.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309671"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuration du compte Azure Batch projet acoustique
Cette procédure décrit comment configurer un compte Azure Batch pour une utilisation avec des intégrations de moteur de projet acoustique Unity et Unreal.

## <a name="get-an-azure-subscription"></a>Obtenir un abonnement Azure
Un [abonnement Azure](https://azure.microsoft.com/free/) est nécessaire avant de configurer des comptes Stockage et Batch. Si vous vous inscrivez pour la première fois, Azure fournit plusieurs ressources gratuites à durée limitée et 200 $ de crédit.

## <a name="create-azure-batch-and-storage-accounts"></a>Créer des comptes de stockage et Azure Batch
Ensuite, suivez [ces instructions](https://docs.microsoft.com/azure/batch/batch-account-create-portal) pour configurer vos comptes Azure Batch et Stockage Azure associés.

Choisissez les options par défaut pour les comptes Batch et Stockage :
  
  ![Capture d’écran d’Azure Batch nouveaux comptes options montrant les paramètres par défaut](media/new-batch-account-create.png)

  ![Capture d’écran de nouveaux comptes de stockage Azure montrant les paramètres par défaut des options](media/batch-storage-account-create.png)

Il faut quelques minutes à Azure pour déployer les comptes. Attendez qu’une notification de fin s’affiche en haut à droite dans le portail.
  
  ![Comptes de la capture d’écran de Azure déploiement notification](media/batch-accounts-deploy-notification.png)

Vos comptes doivent maintenant être visibles sur votre tableau de bord.
  
  ![Tableau de bord portail capture d’écran de Azure montrant un compte de stockage et Batch](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurer l’interface utilisateur de baking acoustique avec des informations d’identification Azure
Cliquez sur le lien vers le compte Batch dans le tableau de bord, puis cliquez sur le lien **Clés** dans la page de compte Batch pour accéder à vos informations d’identification.
  
  ![Compte de capture d’écran d’Azure Batch avec un lien vers la page clés mis en surbrillance](media/batch-access-keys.png)

  ![Page de clés de compte de capture d’écran d’Azure Batch avec les clés d’accès](media/batch-keys-info.png)

Cliquez sur le lien **Compte de stockage** dans la page pour accéder à vos informations d’identification de compte Stockage Azure.
  
  ![Page de clés de compte de capture d’écran de stockage Azure avec des clés d’accès](media/storage-keys-info.png)

Entrez ces informations d’identification dans le [le plug-in de Unity bake](unity-baking.md) ou [plug-in bake Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Types de nœuds et prise en charge de la région
Projet acoustique nécessite la que série Fsv2 et H calcul optimisé des nœuds de machine virtuelle Azure qui ne peuvent pas être pris en charge dans toutes les régions Azure. Vérifiez [ce tableau](https://azure.microsoft.com/global-infrastructure/services) afin d’être sûr de sélectionner le bon emplacement pour votre compte Batch.
![Capture d’écran montrant les Machines virtuelles par région](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Mise à niveau de votre quota
Les comptes Azure Batch sont provisionnés lors de la création du compte avec une limite de 20 cœurs de calcul. Nous souhaiterons peut-être augmenter cette limite pour laquelle cette plus rapides, car vous pouvez paralléliser votre charge de travail acoustique sur plusieurs nœuds, jusqu’au nombre de points de la sonde dans votre scène. Vous pouvez demander une augmentation du quota en cliquant sur le lien **Quota** dans votre page du portail Azure Batch, puis en cliquant sur **Demander une augmentation de quota** :

![Page des quotas de capture d’écran de Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Étapes suivantes
* Intégrer le plug-in de projet acoustiques dans votre [Unity](unity-integration.md) ou [Unreal](unreal-integration.md) projet

