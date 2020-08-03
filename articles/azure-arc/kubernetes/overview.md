---
title: Vue d’ensemble de Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Cet article fournit une vue d’ensemble de Kubernetes avec Azure Arc.
keywords: Kubernetes, Arc, Azure, conteneurs
ms.custom: references_regions
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050054"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Qu’est-ce que Kubernetes avec Azure Arc (préversion) ?

Vous pouvez attacher et configurer des clusters Kubernetes à l’intérieur ou à l’extérieur d’Azure en utilisant Kubernetes avec Azure Arc (préversion). Quand un cluster Kubernetes est attaché à Azure Arc, il s’affiche dans le portail Azure. Il reçoit alors un ID Azure Resource Manager et une identité managée. Les clusters sont attachés à des abonnements Azure standard, sont situés dans un groupe de ressources et peuvent recevoir des étiquettes comme n’importe quelle autre ressource Azure. 

Pour connecter un cluster Kubernetes à Azure, l’administrateur du cluster doit déployer des agents. Ces agents s’exécutent dans un espace de noms Kubernetes nommé `azure-arc` et représentent des déploiements Kubernetes standard. Les agents sont responsables de la connectivité à Azure, de la collecte des journaux et des métriques Azure Arc, et de la surveillance des demandes de configuration. 

Kubernetes avec Azure Arc prend en charge le protocole SSL standard pour sécuriser les données en transit. Par ailleurs, les données sont chiffrées au repos dans une base de données Azure Cosmos DB pour garantir leur confidentialité.
 
> [!NOTE]
> Kubernetes avec Azure Arc est en préversion. Nous ne la recommandons pas pour les charges de travail de production.

## <a name="supported-scenarios"></a>Scénarios pris en charge 

Kubernetes avec Azure Arc prend en charge ces scénarios : 

* Connecter un cluster Kubernetes s’exécutant en dehors d’Azure pour l’inventaire, le regroupement et l’étiquetage.

* Déployer des applications et appliquer une configuration à l’aide de la gestion de la configuration basée sur GitOps. 

* Utiliser Azure Monitor pour conteneurs pour voir et superviser vos clusters. 

* Appliquer des stratégies à l’aide d’Azure Policy pour Kubernetes. 

## <a name="supported-regions"></a>Régions prises en charge 

Kubernetes avec Azure Arc est actuellement pris en charge dans ces régions : 

* USA Est 
* Europe Ouest 

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster](./connect-cluster.md)
