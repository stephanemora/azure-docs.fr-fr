---
title: Offre Azure Application | Microsoft Docs
description: Vue d’ensemble du processus de publication d’une offre Azure Application sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195562"
---
# <a name="azure-application-offer"></a>Offre d’application Azure

Cette section explique comment publier une nouvelle offre Azure Application sur la <a href="https://azuremarketplace.microsoft.com">Place de marché Microsoft Azure</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Chaque application Azure contient un modèle Azure Resource Manager qui définit toutes les ressources techniques utilisées par l’application, qui comprend généralement une ou plusieurs machines virtuelles et d’autres services de prise en charge Azure ou web. | ![Icône Azure Application](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Avantages

Voici quelques-uns des avantages qu’offre la publication de vos applications sur une place de marché Microsoft :

* Cap des 100 millions d’utilisateurs Azure Active Directory atteint par le biais d’Office 365 et de Dynamics 365.

* Extension de votre équipe de vente : atteint des utilisateurs professionnels dans le monde entier et obtient un canal de vente qui implique les utilisateurs finaux, ce qui vous aide à générer des prospects et initie des conversations avec de nouveaux clients dans de multiples secteurs.

* Obtention d’informations détaillées exploitables : nous partagerons des informations détaillées sur les performances de votre application sur AppSource, ce qui fonctionne bien, et les améliorations potentielles à apporter à vos procédures de ventes.

## <a name="types-of-azure-applications"></a>Types d’applications Azure

Il existe deux types d’applications Azure : une application managée et un modèle de solution. Bien que similaires, ces types d’applications présentent des différences importantes.

### <a name="solution-template"></a>Modèle de solution

Les modèles de solution constituent l’un des principaux moyens de publier une solution sur la Place de marché. Ce type d’offre est utilisé si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, au-delà d’une seule machine virtuelle. Vous pouvez automatiser la configuration de plusieurs machines virtuelles à l’aide d’un modèle de solution. Cela inclut la configuration des ressources réseau et des ressources de stockage pour fournir des solutions IaaS complexes. Pour obtenir une vue d’ensemble de exigences liées au modèle de solution ainsi que le modèle de facturation, consultez [Applications Azure : modèles de solution](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Application managée

Une application gérée est similaire à un modèle de solution de la Place de marché à une importante différence près. Dans une application managée, les ressources sont déployées vers un groupe de ressources géré par l’éditeur de l’application. Le groupe de ressources est présent dans l’abonnement du consommateur, mais une identité du locataire de l’éditeur a accès au groupe de ressources. En tant qu’éditeur, vous spécifiez le coût de l’assistance en continu de la solution. Utilisez les applications managées Azure pour créer et fournir facilement des applications clés en main et entièrement managées à vos clients.

Outre la Place de marché, vous pouvez proposer des applications managées dans un catalogue de services. Le catalogue de services est un catalogue interne des solutions approuvées pour les utilisateurs d’une organisation. Vous l’utilisez pour garantir la conformité avec les normes organisationnelles tout en fournissant des solutions aux groupes d’une organisation. Les employés utilisent le catalogue pour découvrir facilement les applications recommandées et approuvées par leur service informatique.

Pour plus d’informations sur les avantages et les différents types d’applications managées, consultez [Vue d’ensemble des applications managées Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Présentation de la publication

La vidéo suivante, [Création de modèles de solutions et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603), est un aperçu de la façon de créer un modèle Azure Resource Manager pour définir une solution d'application Azure, puis de publier l'offre d'application sur la Place de marché Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Flux de travail du processus de publication

Le diagramme suivant montre le processus principal pour la publication d’une offre Azure Application.

![Flux de travail pour la publication d’une offre](./media/new-offer-process.png)

## <a name="offer-components"></a>Composants de l’offre

Cette section décrit les éléments de la publication d’une application managée et elle est conçue comme un guide de publication pour la Place de marché Microsoft Azure. La publication comprend les parties principales suivantes : 

* [Conditions préalables](./cpp-prerequisites.md) : répertorie les exigences techniques et commerciales avant la création ou la publication d’une offre d’application managée. 
* [Créer l’offre](./cpp-create-offer.md) : répertorie les étapes requises pour créer une offre d’application managée à l’aide du Portail Microsoft Cloud Partner. 
* [Publier l’offre](./cpp-publish-offer.md) : explique comment publier l’offre sur la Place de marché Microsoft Azure.

## <a name="steps-in-the-publishing-process"></a>Étapes du processus de publication

Les étapes principales de la publication d’une offre Azure Application sont les suivantes :

1. Créer l’offre : fournissez des informations détaillées sur l’offre. Ces informations incluent : la description de l’offre, les documents marketing, les informations de prise en charge et les spécifications des ressources.

2. Créer les ressources commerciales et techniques : créez les ressources commerciales (documents juridiques et supports marketing) et techniques de la solution associée.

3. Créer la référence SKU : créez la ou les références SKU associées à l’offre. Chaque image que vous prévoyez de publier doit avoir une référence SKU unique.

4. Certifier et publier l’offre : une fois l’offre et les ressources techniques finalisées, vous pouvez envoyer l’offre. Cette soumission commence le processus de publication. Pendant ce processus, la solution est testée, validée et certifiée, puis publiée sur la Place de marché Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

Avant d’envisager ces étapes, vous devez respecter les [exigences techniques et commerciales](./cpp-prerequisites.md) de la publication d’une application managée sur la Place de marché Microsoft Azure.