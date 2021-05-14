---
title: Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure
description: Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 04/28/2021
ms.topic: how-to
ms.openlocfilehash: 5cc99acf303b99e138edd0418d2d49824bbf27fe
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293972"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure

Ce document décrit les étapes de création d’un groupe de serveurs PostgreSQL Hyperscale sur Azure Arc à partir du portail Azure.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Prise en main
Si vous êtes déjà familiarisé avec les rubriques ci-dessous, vous pouvez ignorer ce paragraphe.
Vous souhaiterez peut-être lire les rubriques importantes avant de procéder à la création :
- [Vue d’ensemble des services de données Azure Arc compatibles](overview.md)
- [Modes et exigences de connectivité](connectivity.md)
- [Configuration de stockage et concepts de stockage Kubernetes](storage-configuration.md)
- [Modèle de ressources Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Si vous préférez essayer d’effectuer des opérations sans provisionner un environnement complet, commencez rapidement avec [Démarrage rapide Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou dans une machine virtuelle Azure.


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>Déployer un contrôleur de données Arc configuré pour utiliser le mode de connectivité directe

Condition : avant de déployer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc et le gérer à partir du portail Azure, vous devez d’abord déployer un contrôleur de données Azure Arc configuré pour utiliser le mode de connectivité *directe*.
Pour déployer un contrôleur de données Arc, suivez les instructions fournies dans les articles suivants :
1. [Déployer le contrôleur de données - Mode de connexion directe (Prérequis)](deploy-data-controller-direct-mode-prerequisites.md)
1. [Déployer le contrôleur de données Azure Arc | Mode de connexion directe](deploy-data-controller-direct-mode.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Étape préliminaire et temporaire pour les utilisateurs OpenShift uniquement
Implémentez cette étape avant de passer à l’étape suivante. Pour déployer le groupe de serveurs PostgreSQL Hyperscale sur Red Hat OpenShift dans un projet autre que celui par défaut, vous devez exécuter les commandes suivantes sur votre cluster pour mettre à jour les contraintes de sécurité. Cette commande accorde les privilèges nécessaires aux comptes de service qui exécuteront votre groupe de serveurs PostgreSQL Hyperscale. La contrainte de contexte de sécurité (SCC) arc-data-scc est celle que vous avez ajoutée lors du déploiement du contrôleur de données Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-Group-Name est le nom du groupe de serveurs que vous allez créer à l’étape suivante.**

Pour plus d’informations sur les contraintes de contexte de sécurité dans OpenShift, consultez la documentation [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). 

Passez à l'étape suivante.

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Déployer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure

Afin de déployer et gérer un groupe de serveurs PostgresSQL Hyperscale avec Azure Arc à partir du portail Azure, vous devez déployer ce groupe sur un contrôleur de données Arc configuré pour utiliser le mode de connectivité *directe*. 

> [!IMPORTANT]
> Vous ne pouvez pas gérer un groupe de serveurs PostgresSQL Hyperscale avec Azure Arc depuis le portail Azure si vous l’avez déployé sur un contrôleur de données Azure Arc configuré pour utiliser le mode de connectivité *indirecte*. 

Après avoir déployé un contrôleur de données Arc activé pour le mode de connectivité directe :
1. Ouvrez un navigateur et accédez à l’URL suivante [https://portal.azure.com](https://portal.azure.com).
2. Dans la fenêtre de recherche située en haut de la page, recherchez « *azure arc postgres* » dans la Place de marché Azure, puis sélectionnez **Groupes de serveurs Azure Database pour PostgreSQL - Azure Arc**.
3. Dans la page qui s’ouvre, cliquez sur **+ Créer** dans l’angle supérieur gauche. 
4. Remplissez le formulaire comme vous le faites lorsque vous déployez une autre ressource Azure.


### <a name="important-parameters-you-should-consider-are"></a>Les paramètres importants que vous devez prendre en compte sont les suivants :

- **Nombre de nœuds Worker** que vous souhaitez déployer pour effectuer un scale-out et potentiellement obtenir de meilleures performances. Avant de continuer, lisez les [concepts relatifs à PostgresSQL Hyperscale](concepts-distributed-postgres-hyperscale.md). Par exemple, si vous déployez un groupe de serveurs avec deux nœuds Worker, le déploiement crée trois pods, un pour le nœud/l’instance coordinateur, et deux pour les nœuds/instances Worker (un pour chacun des rôles de travail).

## <a name="next-steps"></a>Étapes suivantes

- Connectez-vous à votre instance PostgreSQL Hyperscale avec Azure Arc : lisez [Obtenir des points de terminaison de connexion et des chaînes de connexion](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- Lisez les concepts et les guides pratiques d’Azure Database pour PostgreSQL Hyperscale pour distribuer vos données sur plusieurs nœuds PostgreSQL Hyperscale et tirer parti d’une amélioration potentielle des performances :
    * [Nœuds et tables](../../postgresql/concepts-hyperscale-nodes.md)
    * [Déterminer le type d’application](../../postgresql/concepts-hyperscale-app-type.md)
    * [Choisir une colonne de distribution](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalisation de table](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuer et modifier des tables](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Concevoir une base de données multilocataire](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Concevoir un tableau de bord d’analytique en temps réel](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Dans les documents ci-dessus, ignorez les sections **Se connecter au portail Azure** et **Créer un serveur Azure Database pour PostgreSQL - Hyperscale (Citus)** . Implémentez les étapes restantes dans votre déploiement Azure Arc. Ces sections sont spécifiques à Azure Database pour PostgreSQL Hyperscale (Citus) proposé en tant que service PaaS dans le cloud Azure, mais les autres parties des documents s’appliquent directement à votre PostgreSQL Hyperscale activé pour Azure Arc.

- [Effectuer un scale-out de votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](scale-out-postgresql-hyperscale-server-group.md)
- [Configuration de stockage et concepts de stockage Kubernetes](storage-configuration.md)
- [Développement des revendications de volume persistant](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modèle de ressources Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


