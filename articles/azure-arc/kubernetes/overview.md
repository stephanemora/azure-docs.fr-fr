---
title: Vue d’ensemble de Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, conteneurs
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665274"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Qu’est-ce que Kubernetes avec Azure Arc (préversion) ?

Vous pouvez joindre et configurer des clusters Kubernetes à l’intérieur ou à l’extérieur d’Azure grâce à Kubernetes avec Azure Arc (préversion). Lorsqu’un cluster Kubernetes est joint à Azure Arc, il s’affiche dans le portail Azure, avec un ID Azure Resource Manager et une identité managée. Les clusters sont joints à des abonnements Azure standard, en direct dans un groupe de ressources, et peuvent recevoir des étiquettes comme n’importe quelle autre ressource Azure. 


La connexion d’un cluster Kubernetes à Azure nécessite qu’un administrateur de cluster déploie des agents. Ces agents s’exécutent dans un espace de noms Kubernetes nommé `azure-arc` et représentent des déploiements Kubernetes standard. Les agents sont responsables de la connectivité à Azure, de la collecte des journaux et des métriques Azure Arc, et de la surveillance des demandes de configuration.  
 
 > [!NOTE]
> Kubernetes avec Azure Arc est en préversion et n’est pas recommandé pour les charges de travail de production. 


## <a name="supported-scenarios"></a>Scénarios pris en charge 

Kubernetes avec Azure Arc prend en charge les scénarios suivants : 

* Connexion de Kubernetes en cours d’exécution en dehors d’Azure pour l’inventaire, le regroupement et l’étiquetage 

* Déployer des applications et appliquer une configuration à l’aide de la gestion de la configuration basée sur GitOps 

* Utiliser Azure Monitor pour conteneurs pour afficher et surveiller vos clusters 

* Appliquer des stratégies à l’aide d’Azure Policy pour Kubernetes 

 
## <a name="supported-regions"></a>Régions prises en charge 

Kubernetes avec Azure Arc est actuellement pris en charge dans les régions suivantes : 

* USA Est 
* Europe Ouest 


## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster](./connect-cluster.md)
