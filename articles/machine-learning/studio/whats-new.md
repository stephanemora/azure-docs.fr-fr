---
title: Nouveautés d’Azure Machine Learning Studio | Microsoft Docs
description: De nouvelles fonctionnalités sont disponibles dans Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: c93bb9a2fa7991df265b4767585d920c137a436d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311141"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Nouveautés d’Azure Machine Learning Studio

## <a name="october-2018"></a>Octobre 2018

Le moteur de langage R dans le module [Execute R Script](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) comprend une nouvelle version du runtime R : Microsoft R Open (MRO) 3.4.4. MRO 3.4.4 est basé sur du code open source CRAN R 3.4.4. Il est donc compatible avec les packages qui fonctionnent avec cette version de R. Pour plus d’informations sur les packages R pris en charge, consultez l’article « [Packages R pris en charge par Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List) ».

## <a name="march-2017"></a>Mars 2017 
Cette mise à jour de Microsoft Azure Machine Learning comprend la fonctionnalité suivante :

* Capacité dédiée pour les travaux du service d’exécution de lot d’Azure Machine Learning

    Le traitement par pool Batch de Machine Learning utilise le [service Azure Batch](../../batch/batch-technical-overview.md) pour fournir une échelle gérée par le client pour le service d’exécution de lot d’Azure Machine Learning. Le traitement par pool Batch vous permet de créer des pools Azure Batch, dans lesquels vous pouvez soumettre des travaux par lots, en vous assurant qu’ils s’exécutent de manière prévisible.

    Pour en savoir plus, voir [Service Azure Batch pour les travaux Machine Learning](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>Août 2016 
Cette mise à jour de Microsoft Azure Machine Learning comprend les fonctionnalités suivantes :
* Vous pouvez désormais gérer Les services web Machine via le nouveau portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/) qui fournit un emplacement pour gérer tous les aspects de votre service web.    
  * Fournit des [statistiques d’utilisation](manage-new-webservice.md) du service web.
  * Simplifie le test des appels de demande distante d’Azure Machine Learning à l’aide d’exemples de données.
  * Fournit une nouvelle page de test de service d’exécution de lot avec des exemples de données et un historique des travaux soumis.
  * Fournit une gestion de point de terminaison simplifiée.

## <a name="july-2016"></a>Juillet 2016 
Cette mise à jour de Microsoft Azure Machine Learning comprend les fonctionnalités suivantes :
* Les services web sont désormais gérés en tant que ressources Azure via les interfaces [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , ce qui permet les améliorations suivantes :
  * De nouvelles [API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) pour déployer et gérer vos services web basés sur Resource Manager.
  * Un nouveau portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/) qui fournit un emplacement pour gérer tous les aspects de votre service web.
* Intègre un nouveau modèle de déploiement de service web multi-régions et basé sur un abonnement, qui utilise des API basées sur Resource Manager afin d’exploiter le fournisseur de ressources Resource Manager pour les services web.
* Présente de nouveaux [plans de tarification](https://azure.microsoft.com/pricing/details/machine-learning/) et des capacités de gestion en utilisant le nouveau fournisseur de ressources Resource Manager pour la facturation.
  * Vous pouvez à présent [déployer votre service web dans plusieurs régions](how-to-deploy-to-multiple-regions.md) sans avoir à créer un abonnement dans chaque région.
* Fournit des [statistiques d’utilisation](manage-new-webservice.md)du service web.
* Simplifie le test des appels de demande distante d’Azure Machine Learning à l’aide d’exemples de données.
* Fournit une nouvelle page de test de service d’exécution de lot avec des exemples de données et un historique des travaux soumis.

En outre, Machine Learning Studio a été mis à jour afin que vous puissiez déployer vers le nouveau modèle de service web ou continuer à déployer vers le modèle de service web classique. 

