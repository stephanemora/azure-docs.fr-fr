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
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: d3b761630124ef7f72269fe0712bf22647968d59
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137026"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuration du compte Azure Batch projet acoustique
Cette procédure décrit comment configurer un compte Azure Batch pour une utilisation avec des intégrations de moteur de projet acoustique Unity et Unreal.

## <a name="get-an-azure-subscription"></a>Obtenir un abonnement Azure
Un [abonnement Azure](https://azure.microsoft.com/free/) est nécessaire avant de configurer des comptes Stockage et Batch. Si vous vous inscrivez pour la première fois, Azure fournit plusieurs ressources gratuites à durée limitée et 200 $ de crédit.

## <a name="create-azure-batch-and-storage-accounts"></a>Créer des comptes de stockage et Azure Batch
Ensuite, suivez [ces instructions](https://docs.microsoft.com/azure/batch/batch-account-create-portal) pour configurer vos comptes Azure Batch et Stockage Azure associés.

Choisissez les options par défaut pour les comptes Batch et Stockage :
  
  ![Nouveau compte Batch](media/new-batch-account-create.png)

  ![Nouveau compte Stockage](media/batch-storage-account-create.png)

Il faut quelques minutes à Azure pour déployer les comptes. Attendez qu’une notification de fin s’affiche en haut à droite dans le portail.
  
  ![Comptes déployés](media/batch-accounts-deploy-notification.png)

Vos comptes doivent maintenant être visibles sur votre tableau de bord.
  
  ![Tableau de bord du portail](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurer l’interface utilisateur de baking acoustique avec des informations d’identification Azure
Cliquez sur le lien vers le compte Batch dans le tableau de bord, puis cliquez sur le lien **Clés** dans la page de compte Batch pour accéder à vos informations d’identification.
  
  ![Lien Clés dans Batch](media/batch-access-keys.png)

  ![Informations d’identification de compte Batch](media/batch-keys-info.png)

Cliquez sur le lien **Compte de stockage** dans la page pour accéder à vos informations d’identification de compte Stockage Azure.
  
  ![Informations d’identification du compte de stockage](media/storage-keys-info.png)

Entrez ces informations d’identification dans le [le plug-in de Unity bake](unity-baking.md) ou [plug-in bake Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Types de nœuds et prise en charge de la région
Projet acoustique nécessite la que série Fsv2 et H calcul optimisé des nœuds de machine virtuelle Azure qui ne peuvent pas être pris en charge dans toutes les régions Azure. Vérifiez [ce tableau](https://azure.microsoft.com/global-infrastructure/services) afin d’être sûr de sélectionner le bon emplacement pour votre compte Batch.
![Machines virtuelles Azure par région](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Mise à niveau de votre quota
Les comptes Azure Batch sont provisionnés lors de la création du compte avec une limite de 20 cœurs de calcul. Nous souhaiterons peut-être augmenter cette limite pour laquelle cette plus rapides, car vous pouvez paralléliser votre charge de travail acoustique sur plusieurs nœuds, jusqu’au nombre de points de la sonde dans votre scène. Vous pouvez demander une augmentation du quota en cliquant sur le lien **Quota** dans votre page du portail Azure Batch, puis en cliquant sur **Demander une augmentation de quota** :

![Augmentation de quota Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Étapes suivantes
* Intégrer le plug-in de projet acoustiques dans votre [Unity](unity-integration.md) ou [Unreal](unreal-integration.md) projet

