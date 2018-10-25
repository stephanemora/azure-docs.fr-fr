---
title: Configurer des comptes Azure pour Project Acoustics
titlesuffix: Azure Cognitive Services
description: Suivez ce guide pour configurer des comptes Azure Batch et Stockage Azure nécessaires à l’utilisation d’Acoustics.
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 05f7c435ca61930da6270c54e71c784f1b7aebcc
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900614"
---
# <a name="create-an-azure-batch-account"></a>Créer un compte Azure Batch
Suivez ce guide pour configurer des comptes Azure Batch et Stockage Azure nécessaires à l’utilisation d’Acoustics. Pour plus d’informations sur le plug-in Unity développé dans le cadre de Project Acoustics, consultez la [Présentation d’Acoustics](what-is-acoustics.md). Pour plus d’informations sur la façon d’incorporer Acoustics dans votre projet Unity, consultez [Bien démarrer](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Obtenir un abonnement Azure
Un [abonnement Azure](https://azure.microsoft.com/free/) est nécessaire avant de configurer des comptes Stockage et Batch. Si vous vous inscrivez pour la première fois, Azure fournit plusieurs ressources gratuites à durée limitée et 200 $ de crédit.

## <a name="create-azure-batch-and-storage-accounts"></a>Créer des comptes de stockage et Azure Batch
Ensuite, suivez [ces instructions](https://docs.microsoft.com/azure/batch/batch-account-create-portal) pour configurer vos comptes Azure Batch et Stockage Azure associés.

Choisissez les options par défaut pour les comptes Batch et Stockage :
  
  ![Nouveau compte Batch](media/NewBatchAccountCreate.png)

  ![Nouveau compte Stockage](media/BatchStorageAccountCreate.png)

Il faut quelques minutes à Azure pour déployer les comptes. Attendez qu’une notification de fin s’affiche en haut à droite dans le portail.
  
  ![Comptes déployés](media/BatchAccountsDeployNotification.png)

Vos comptes doivent maintenant être visibles sur votre tableau de bord.
  
  ![Tableau de bord du portail](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurer l’interface utilisateur de baking acoustique avec des informations d’identification Azure
Cliquez sur le lien vers le compte Batch dans le tableau de bord, puis cliquez sur le lien **Clés** dans la page de compte Batch pour accéder à vos informations d’identification.
  
  ![Lien Clés dans Batch](media/BatchAccessKeys.png)

  ![Informations d’identification de compte Batch](media/BatchKeysInfo.png)

Cliquez sur le lien **Compte de stockage** dans la page pour accéder à vos informations d’identification de compte Stockage Azure.
  
  ![Informations d’identification du compte de stockage](media/StorageKeysInfo.png)

Entrez ces informations d’identification sous l’onglet Bake comme décrit dans la [procédure pas à pas dans l’interface utilisateur de baking](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Types de nœuds et prise en charge de la région
Project Acoustics nécessite des nœuds de machines virtuelles Azure optimisés pour le calcul de série F et H qui risquent de ne pas être pris en charge dans toutes les régions Azure. Vérifiez [ce tableau](https://azure.microsoft.com/global-infrastructure/services) afin d’être sûr de sélectionner le bon emplacement pour votre compte Batch. À l’heure actuelle, les machines virtuelles de série H sont prises en charge dans les régions USA Est, USA Centre Nord, USA Centre Sud, USA Ouest, USA Ouest 2, Europe Nord, Europe Ouest et Japon Ouest.

## <a name="upgrading-your-quota"></a>Mise à niveau de votre quota
Les comptes Azure Batch sont provisionnés lors de la création du compte avec une limite de 20 cœurs de calcul. Vous souhaiterez peut-être augmenter cette limite afin d’accélérer le baking, car vous pouvez paralléliser votre charge de travail acoustique sur plusieurs nœuds, jusqu’au nombre de points de sonde dans votre scène. Vous pouvez demander une augmentation du quota en cliquant sur le lien **Quota** dans votre page du portail Azure Batch, puis en cliquant sur **Demander une augmentation de quota** :

![Augmentation de quota Azure](media/azurequotas.png)

## <a name="next-steps"></a>Étapes suivantes
* Bien démarrer avec [l’intégration acoustique dans votre projet Unity](getting-started.md)
* Explorer l’[exemple de scène](sample-walkthrough.md)

