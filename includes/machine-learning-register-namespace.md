---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623548"
---
* Lors de la création d’un espace de travail, vous pouvez soit créer automatiquement les services nécessaires à l’espace de travail, soit utiliser des services existants. Si vous voulez utiliser des __services existants d’un autre abonnement Azure__ que l’espace de travail, vous devez inscrire l’espace de noms Azure Machine Learning dans l’abonnement qui contient ces services. Par exemple, la création d’un espace de travail dans l’abonnement A qui utilise un compte de stockage de l’abonnement B, l’espace de noms Azure Machine Learning doit être inscrit dans l’abonnement B pour que vous puissiez utiliser le compte de stockage avec l’espace de travail.

    Le fournisseur de ressources pour Azure Machine Learning est __Microsoft.MachineLearningServices__. Pour plus d’informations sur la façon de déterminer s’il est inscrit et de l’inscrire, consultez l’article [Fournisseurs et types de ressources Azure](../articles/azure-resource-manager/management/resource-providers-and-types.md).

    > [!IMPORTANT]
    > Cela s’applique uniquement aux ressources fournies lors de la création de l’espace de travail : comptes de stockage Azure, registre de conteneurs Azure, Azure Key Vault et Application Insights.
