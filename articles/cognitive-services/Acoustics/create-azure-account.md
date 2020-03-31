---
title: Configuration du compte Project Acoustics Azure Batch
titlesuffix: Azure Cognitive Services
description: Cette procédure décrit comment configurer un compte Azure Batch pour une utilisation avec des intégrations de moteur Project Acoustics Unity et Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855077"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuration du compte Project Acoustics Azure Batch
Cette procédure décrit comment configurer un compte Azure Batch pour une utilisation avec des intégrations de moteur Project Acoustics Unity et Unreal.

## <a name="get-an-azure-subscription"></a>Obtenir un abonnement Azure
Un [abonnement Azure](https://azure.microsoft.com/free/) est nécessaire avant de configurer des comptes Stockage et Batch. Si vous vous inscrivez pour la première fois, Azure fournit plusieurs ressources gratuites à durée limitée et 200 $ de crédit.

## <a name="create-azure-batch-and-storage-accounts"></a>Créer des comptes de stockage et Azure Batch
Ensuite, suivez [ces instructions](https://docs.microsoft.com/azure/batch/batch-account-create-portal) pour configurer vos comptes Azure Batch et Stockage Azure associés.

Choisissez les options par défaut pour les comptes Batch et Stockage :
  
  ![Capture d’écran d’options de nouveaux comptes Azure Batch montrant les paramètres par défaut](media/new-batch-account-create.png)

  ![Capture d’écran d’options de nouveaux comptes de stockage Azure montrant les paramètres par défaut](media/batch-storage-account-create.png)

Il faut quelques minutes à Azure pour déployer les comptes. Attendez qu’une notification de fin s’affiche en haut à droite dans le portail.
  
  ![Capture d’écran de la notification de déploiement de comptes Azure](media/batch-accounts-deploy-notification.png)

Vos comptes doivent maintenant être visibles sur votre tableau de bord.
  
  ![Capture d’écran du tableau de bord du portail Azure montrant un compte Batch et un compte de stockage](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurer l’interface utilisateur de baking acoustique avec des informations d’identification Azure
Cliquez sur le lien vers le compte Batch dans le tableau de bord, puis cliquez sur le lien **Clés** dans la page de compte Batch pour accéder à vos informations d’identification.
  
  ![Capture d’écran d’un compte Azure Batch avec un lien vers la page des clés mis en surbrillance](media/batch-access-keys.png)

  ![Capture d’écran de la page des clés d’un compte Azure Batch avec les clés d’accès](media/batch-keys-info.png)

Cliquez sur le lien **Compte de stockage** dans la page pour accéder à vos informations d’identification de compte Stockage Azure.
  
  ![Capture d’écran de la page des clés d’un compte de stockage Azure avec les clés d’accès](media/storage-keys-info.png)

Entrez ces informations d’identification dans le [plug-in Unity bake](unity-baking.md) ou le [plug-in bake Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Types de nœuds et prise en charge de la région
Project Acoustics nécessite des nœuds de machines virtuelles Azure optimisés pour le calcul de série Fsv2 et H qui risquent de ne pas être pris en charge dans toutes les régions Azure. Vérifiez [ce tableau](https://azure.microsoft.com/global-infrastructure/services) afin d’être sûr de sélectionner le bon emplacement pour votre compte Batch.
![Capture d’écran montrant les machines virtuelles Azure par région](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Mise à niveau de votre quota
Les comptes Azure Batch sont provisionnés lors de la création du compte avec une limite de 20 cœurs de calcul. Vous souhaiterez peut-être augmenter cette limite afin d’accélérer le baking, car vous pouvez paralléliser votre charge de travail acoustique sur plusieurs nœuds, jusqu’au nombre de points de sonde dans votre scène. Vous pouvez demander une augmentation du quota en cliquant sur le lien **Quota** dans votre page du portail Azure Batch, puis en cliquant sur **Demander une augmentation de quota** :

![Capture d’écran de la page de quotas Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Étapes suivantes
* Intégrer le plug-in Project Acoustics dans votre projet [Unity](unity-integration.md) ou [Unreal](unreal-integration.md)

