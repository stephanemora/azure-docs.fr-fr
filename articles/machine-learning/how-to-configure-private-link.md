---
title: Configuration d’un point de terminaison privé (préversion)
titleSuffix: Azure Machine Learning
description: Utilisez Azure Private Link pour accéder en toute sécurité à votre espace de travail Azure Machine Learning à partir d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: 83927c9df9a4f1a6ab32c15c481898ec68f53c4c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898138"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configuration d’Azure Private Link pour un espace de travail Azure Machine Learning (préversion)

Dans ce document, vous allez apprendre à utiliser Azure Private Link avec votre espace de travail Azure Machine Learning. Pour plus d’informations sur la configuration d’un réseau virtuel pour Azure Machine Learning, consultez [Vue d’ensemble de l’isolement et la confidentialité des réseaux virtuels](how-to-network-security-overview.md)

> [!IMPORTANT]
> L’utilisation d’Azure Private Link avec un espace de travail Azure Machine Learning est actuellement en préversion publique. Cette fonctionnalité n’est disponible que dans les régions **USA Est**, **USA Centre Sud** et **USA Ouest 2**. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Private Link vous permet de vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à votre espace de travail pour qu’il ne se fasse que sur les adresses IP privées. Azure Private Link permet de réduire le risque d’exfiltration des données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Azure Private Link n’a pas d’effet sur le plan de contrôle Azure (opérations de gestion), comme la suppression de l’espace de travail ou la gestion des ressources de calcul. Par exemple, la création, la mise à jour ou la suppression d’une cible de calcul. Ces opérations sont effectuées sur l’Internet public comme d’habitude. Les opérations de plan de données telles que l’utilisation du studio Azure Machine Learning, des API (y compris les pipelines publiés) ou du SDK utilisent le point de terminaison privé.
>
> Il se peut que vous rencontriez des problèmes en tentant d’accéder au point de terminaison privé de votre espace de travail si vous utilisez Mozilla Firefox. Un de ces problème peut être lié à DNS sur HTTPS dans Mozilla. Nous vous recommandons d’utiliser Microsoft Edge ou Google Chrome comme solution de contournement.

> [!TIP]
> L’instance de calcul Azure Machine Learning peut être utilisée avec un espace de travail et un point de terminaison privé. Cette capacité est actuellement disponible en préversion publique dans les régions **USA Est**, **USA Centre Sud** et **USA Ouest 2**.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Créer un espace de travail qui utilise un point de terminaison privé

> [!IMPORTANT]
> Actuellement, nous prenons uniquement en charge l’activation d’un point de terminaison privé lors de la création d’un espace de travail Azure Machine Learning.

Vous pouvez utiliser le modèle [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) pour créer un espace de travail avec un point de terminaison privé.

Pour plus d’informations sur l’utilisation de ce modèle, y compris les points de terminaison privés, consultez [Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Utilisation d’un espace de travail sur un point de terminaison privé

Étant donné que les communications avec l’espace de travail sont autorisées uniquement à partir du réseau virtuel, tous les environnements de développement qui utilisent l’espace de travail doivent être faire partie du réseau virtuel. Par exemple, une machine virtuelle sur le réseau virtuel.

> [!IMPORTANT]
> Pour éviter toute interruption temporaire de la connectivité, Microsoft recommande de vider le cache DNS sur les ordinateurs qui se connectent à l’espace de travail après avoir activé Azure Private Link. 

Pour plus d’informations sur Machines virtuelles Microsoft Azure, consultez la [documentation relative à Machines Virtuelles](/azure/virtual-machines/).


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation de votre espace de travail Azure Machine Learning, consultez l’article [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md).
