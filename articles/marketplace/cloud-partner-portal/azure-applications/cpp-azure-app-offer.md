---
title: Offre Azure Application | Place de marché Azure
description: Vue d’ensemble du processus de publication d’une offre Azure Application sur la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285314"
---
# <a name="azure-application-offer"></a>Offre d’application Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Cette section explique comment publier une nouvelle application Azure sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com).  Chaque application Azure contient un modèle Azure Resource Manager qui définit toutes les ressources techniques utilisées par l’application, qui comprend généralement une ou plusieurs machines virtuelles et d’autres services de prise en charge Azure ou web. Toutes les offres d’application Azure doivent activer la sécurité d’accès via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Icône Azure Application](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Présentation de la publication

La vidéo suivante, intitulée [Création de modèles de solution et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603), est une introduction. Elle présente les types d’offre disponibles, les ressources techniques nécessaires, la façon de créer un modèle Azure Resource Manager, le développement et les tests de l’interface utilisateur de l’application, la façon de publier l’offre d’application et le processus de révision de l’application.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Types d’applications Azure

Il existe deux types d’applications Azure : les applications managées et les modèles de solution. 

- Les modèles de solution constituent l’un des principaux moyens de publier une solution sur la Place de marché. Ce type d’offre est utilisé si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, au-delà d’une seule machine virtuelle. Vous pouvez automatiser la configuration de plusieurs machines virtuelles à l’aide d’un modèle de solution. Cette automatisation inclut la configuration des ressources réseau et des ressources de stockage pour fournir des solutions IaaS complexes. Pour obtenir une vue d’ensemble de exigences liées au modèle de solution ainsi que le modèle de facturation, consultez [Applications Azure : modèles de solution](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Les applications managées sont similaires aux modèles de solution, à une importante différence près. Dans une application managée, les ressources sont déployées vers un groupe de ressources géré par l’éditeur de l’application. Le groupe de ressources est présent dans l’abonnement du consommateur, mais une identité du locataire de l’éditeur a accès au groupe de ressources. En tant qu’éditeur, vous spécifiez le coût de l’assistance en continu de la solution. Utilisez les applications managées Azure pour créer et fournir facilement des applications clés en main et entièrement managées à vos clients.

Outre la Place de marché Microsoft Azure, vous pouvez proposer des applications managées dans un catalogue de services. Le catalogue de services est un catalogue interne des solutions approuvées pour les utilisateurs d’une organisation. Vous l’utilisez pour garantir la conformité avec les normes organisationnelles tout en fournissant des solutions aux groupes d’une organisation. Les employés utilisent le catalogue pour découvrir facilement les applications recommandées et approuvées par leur service informatique.

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](../../cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

Pour plus d’informations sur les avantages et les différents types d’applications managées, consultez [Vue d’ensemble des applications managées Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Flux de travail du processus de publication

Le diagramme suivant montre le processus principal pour la publication d’une offre Azure Application.

![Flux de travail pour la publication d’une offre](./media/new-offer-process.png)

Les étapes principales de la publication d’une offre Azure Application sont les suivantes :

1. Respecter les [conditions préalables](./cpp-prerequisites.md) : (Non affiché) Vérifiez que vous avez respecté les exigences commerciales et techniques pour la publication d’une application Azure sur la Place de marché. 

1. [Créer l’offre](./cpp-create-offer.md) : fournissez des informations détaillées sur l’offre. Ces informations incluent : la description de l’offre, les documents marketing, les informations de prise en charge et les spécifications des ressources.

1. [Créer ou rassembler les ressources commerciales et techniques](./cpp-create-technical-assets.md) : créez les ressources commerciales (documents juridiques et supports marketing) et techniques de la solution associée.

1. [Créer la référence SKU](./cpp-skus-tab.md) : créez la ou les références SKU associée(s) à l’offre. Chaque image que vous prévoyez de publier doit avoir une référence SKU unique.

1. [Certifier et publier l’offre](./cpp-publish-offer.md) : une fois l’offre et les ressources techniques finalisées, vous pouvez envoyer l’offre. Cette soumission commence le processus de publication. Pendant ce processus, la solution est testée, validée et certifiée, puis publiée sur la Place de marché Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

Avant d’envisager ces étapes, vous devez respecter les [exigences techniques et commerciales](./cpp-prerequisites.md) de la publication d’une application managée sur la Place de marché Microsoft Azure.
