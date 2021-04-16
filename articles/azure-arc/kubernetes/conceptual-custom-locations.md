---
title: Emplacements personnalisés - Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité Emplacements personnalisés de Kubernetes avec Azure Arc.
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450726"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Emplacements personnalisés sur Kubernetes avec Azure Arc

En tant qu’extension de la construction d’emplacement Azure, les *Emplacements personnalisés* permettent aux administrateurs de locataire d’utiliser leurs clusters Kubernetes avec Azure Arc comme emplacements cibles pour le déploiement d’instances de services Azure. Les exemples de ressources Azure incluent SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc.

À l’instar des emplacements Azure, les utilisateurs finaux au sein du locataire disposant d’un accès aux emplacements personnalisés peuvent y déployer des ressources à l’aide de la capacité de calcul privée de leur entreprise.

[ ![Couches de la plateforme Arc](./media/conceptual-arc-platform-layers.png) ](./media/conceptual-arc-platform-layers.png#lightbox)

Vous pouvez visualiser les emplacements personnalisés sous la forme d’une couche d’abstraction sur des extensions de cluster, une connexion au cluster et un cluster Kubernetes avec Azure Arc. Les emplacements personnalisés créent les [RoleBindings et ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) précis dont d’autres services Azure ont besoin pour accéder au cluster. Ces autres services Azure nécessitent un accès au cluster pour gérer les ressources que le client souhaite déployer sur ses clusters.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

Lorsque l’administrateur active la fonctionnalité Emplacements personnalisés sur le cluster, un ClusterRoleBinding est créé sur le cluster, autorisant l’application Azure AD utilisée par le fournisseur de ressources d’emplacements personnalisés. Une fois autorisé, le fournisseur de ressources d’emplacements personnalisés peut créer des ClusterRoleBindings ou des RoleBindings nécessaires à d’autres fournisseurs de ressources Azure pour créer des ressources personnalisées sur ce cluster. Les extensions de cluster installées sur le cluster déterminent la liste des fournisseurs de ressources à autoriser.

[ ![Utiliser des emplacements personnalisés](./media/conceptual-custom-locations-usage.png) ](./media/conceptual-custom-locations-usage.png#lightbox)

Lorsque l’utilisateur crée une instance de service de données sur le cluster : 
1. La requête PUT est envoyée à Azure Resource Manager.
1. La requête PUT est transférée au fournisseur de ressources de services de données avec Azure Arc. 
1. Le fournisseur de ressources récupère le fichier `kubeconfig` associé au cluster Kubernetes avec Azure Arc, sur lequel se trouve l’emplacement personnalisé. 
   * L’emplacement personnalisé est référencé comme `extendedLocation` dans la requête PUT d’origine. 
1. Le fournisseur de ressources de services de données avec Azure Arc utilise `kubeconfig` pour communiquer avec le cluster afin de créer une ressource personnalisée du type Services de données avec Azure Arc sur l’espace de noms mappé à l’emplacement personnalisé. 
   * L’opérateur Services de données avec Azure Arc a été déployé via la création d’une extension de cluster avant l’emplacement personnalisé. 
1. L’opérateur Services de données avec Azure Arc lit la nouvelle ressource personnalisée créée sur le cluster, et crée le contrôleur de données, ce qui se traduit par la réalisation de l’état souhaité sur le cluster. 

La séquence des étapes de création de l’instance managée SQL et de l’instance PostgreSQL est identique à celle décrite ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Créez un emplacement personnalisé](./custom-locations.md) sur votre cluster Kubernetes avec Azure Arc.