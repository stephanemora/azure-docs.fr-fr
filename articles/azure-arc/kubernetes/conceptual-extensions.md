---
title: Extensions de cluster – Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité d’extensions de cluster de Kubernetes avec Azure Arc
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450752"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Extensions de cluster sur Kubernetes avec Azure Arc

Les [graphiques Helm](https://helm.sh/) vous aident à gérer les applications Kubernetes en fournissant les blocs de construction nécessaires pour définir, installer et mettre à niveau les applications Kubernetes les plus complexes. La fonctionnalité d’extension de cluster cherche à construire sur les composants d’empaquetage de Helm. Pour ce faire, elle fournit une expérience pilotée par Azure Resource Manager pour l’installation et la gestion du cycle de vie d’extensions de cluster telles que Azure Monitor et Azure Defender pour Kubernetes. Par rapport à ce qui est déjà disponible en mode natif avec les graphiques Helm, la fonctionnalité d’extensions de cluster offre les avantages supplémentaires suivants :

- Obtenir un inventaire de l’ensemble des clusters et des extensions installés sur ceux-ci.
- Utiliser Azure Policy pour automatiser le déploiement à grande échelle d’extensions de cluster.
- S’abonner aux trains de version de chaque extension.
- Configurer une mise à niveau automatique des extensions.
- Prise en charge de la création d’instance d’extension et des événements de mise à jour et de suppression de la gestion du cycle de vie.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

[ ![Architecture des extensions de cluster](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

L’instance d’extension de cluster est créée en tant que ressource Azure Resource Manager d’extension (`Microsoft.KubernetesConfiguration/extensions`) en plus de la ressource Kubernetes avec Azure Arc (représentée par `Microsoft.Kubernetes/connectedClusters`) dans Azure Resource Manager. Une représentation dans Azure Resource Manager vous permet de créer une stratégie qui vérifie toutes les ressources Kubernetes avec Azure Arc, avec ou sans extension de cluster spécifique. Une fois que vous avez déterminé quels clusters ne disposent pas d’extensions de cluster avec les valeurs de propriété souhaitées, vous pouvez corriger ces ressources non conformes à l’aide d’Azure Policy.

Le composant `config-agent` s’exécutant dans votre cluster effectue le suivi des ressources d’extension nouvelles ou mises à jour sur la ressource Kubernetes avec Azure Arc. Le composant `extensions-manager` s’exécutant dans votre cluster extrait le graphique Helm à partir d’Azure Container Registry ou de Microsoft Container Registry, et l’installe sur le cluster. 

Les composants `config-agent` et `extensions-manager` s’exécutant dans le cluster gèrent les mises à jour de version et la suppression d’instance d’extension.

> [!NOTE]
> * Le composant `config-agent` supervise la disponibilité des ressources d’extension nouvelles ou mises à jour sur la ressource Kubernetes avec Arc. Les agents ont donc besoin d’une connectivité pour que l’état souhaité soit extrait vers le cluster. Si les agents ne parviennent pas à se connecter à Azure, il se produit un retard dans la propagation de l’état souhaité vers le cluster.
> * Les paramètres de configuration protégée pour une extension sont stockés pendant jusqu’à 48 heures dans les services Kubernetes avec Azure Arc. Par conséquent, si le cluster reste déconnecté pendant les 48 heures après la création de la ressource d’extension sur Azure, l’extension passe d’un état `Pending` à un état `Failed`. Nous vous recommandons de mettre les clusters en ligne aussi régulièrement que possible.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Déployez des extensions de cluster](./extensions.md) sur votre cluster Kubernetes avec Azure Arc.