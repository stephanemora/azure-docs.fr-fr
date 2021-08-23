---
title: Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure
description: Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc à partir du portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: 905bfa7093fc21dfe472742e03d938cbfcaee43a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111407576"
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

Une fois que vous avez déployé un contrôleur de données Arc activé pour le mode de connectivité directe, vous pouvez choisir l’une des trois options suivantes qui permettent de déployer un groupe de serveurs Postgres Hyperscale avec Azure Arc :

### <a name="option-1-deploy-from-the-azure-marketplace"></a>Option 1 : Effectuer un déploiement à partir de la Place de marché Azure
1. Ouvrez un navigateur et accédez à l’URL suivante [https://portal.azure.com](https://portal.azure.com)
2. Dans la fenêtre de recherche située en haut de la page, recherchez « *azure arc postgres* » dans la Place de marché Azure, puis sélectionnez **Groupes de serveurs Azure Database pour PostgreSQL - Azure Arc**.
3. Dans la page qui s’ouvre, cliquez sur **+ Créer** dans l’angle supérieur gauche. 
4. Remplissez le formulaire comme vous le faites lorsque vous déployez une autre ressource Azure.

### <a name="option-2-deploy-from-the-azure-database-for-postgresql-deployment-option-page"></a>Option 2 : Déployer à partir de la page d’options de déploiement d’Azure Database pour PostgreSQL
1. Ouvrez un navigateur et accédez à l’URL suivante https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer.
2. Cliquez sur la vignette en bas à droite. Elle s’intitule : PostgreSQL Hyperscale avec Azure Arc (Préversion).
3. Remplissez le formulaire comme vous le faites lorsque vous déployez une autre ressource Azure.

### <a name="option-3-deploy-from-the-azure-arc-center"></a>Option 3 : Effectuer un déploiement à partir du centre Azure Arc
1. Ouvrez un navigateur et accédez à l’URL suivante https://ms.portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/overview.
1. Au centre de la page, cliquez sur [Déployer], sous la vignette intitulée *Déployer des services Azure*, puis cliquez sur [Déployer] dans la vignette intitulée PostgreSQL Hyperscale (Préversion).
2. Autrement, dans le volet de navigation situé à gauche dans la page, sous la section Services, cliquez sur [PostgreSQL Hyperscale (Préversion)], puis cliquez sur [+ Créer] en haut à gauche du volet.


#### <a name="important-parameters-you-should-consider"></a>Paramètres importants à prendre en compte :

- **Nombre de nœuds Worker** que vous souhaitez déployer pour effectuer un scale-out et potentiellement obtenir de meilleures performances. Avant de continuer, lisez les [concepts relatifs à Postgres Hyperscale](concepts-distributed-postgres-hyperscale.md). Le tableau ci-dessous indique la plage des valeurs prises en charge et le type de déploiement Postgres obtenu avec ces valeurs. Par exemple, si vous souhaitez déployer un groupe de serveurs avec 2 nœuds Worker, indiquez 2. Cette opération crée trois pods, un pour le nœud coordinateur/l’instance et deux pour les nœuds Worker/instances (un pour chacun des Workers).



|Vous devez avoir   |Forme du groupe de serveurs que vous allez déployer   |Nombre de nœuds Worker à indiquer   |Notes   |
|---|---|---|---|
|Une forme de Postgres avec scale-out pour répondre aux besoins de scalabilité de vos applications.   |3 instances Postgres ou plus, 1 est coordinateur, n sont des Workers avec n >=2.   |n, avec n >=2.   |L’extension Citus qui fournit la capacité Hyperscale est chargée.   |
|Une forme de base de Postgres Hyperscale permettant la validation fonctionnelle de votre application à un coût minimal. Non valide pour la validation des performances et de la scalabilité. Pour cela, vous devez utiliser le type de déploiement décrit ci-dessus.   |1 instance Postgres qui est à la fois coordinateur et Worker.   |0 et ajoutez Citus à la liste des extensions à charger.   |L’extension Citus qui fournit la capacité Hyperscale est chargée.   |
|Une instance simple de Postgres prête pour un scale-out quand vous en avez besoin.   |1 instance Postgres. La sémantique du coordinateur et du Worker n’est pas encore déterminée. Pour effectuer un scale-out après le déploiement, modifiez la configuration, augmentez le nombre de nœuds Worker et distribuez les données.   |0   |L’extension Citus qui fournit la capacité Hyperscale est présente dans votre déploiement, mais elle n’est pas encore chargée.   |
|   |   |   |   |

Si vous indiquez 1 Worker, le déploiement fonctionne mais nous vous déconseillons de l’utiliser. Ce déploiement vous apporte peu de valeur. Cette configuration vous permet d’obtenir 2 instances de Postgres : 1 coordinateur et 1 Worker. Avec cette configuration, vous n’effectuez pas le scale-out des données, car vous déployez un seul Worker. Par conséquent, le niveau de performances et de scalabilité n’augmente pas. La prise en charge de ce déploiement sera supprimée dans une version ultérieure.

- **Les classes de stockage** que vous souhaitez que votre groupe de serveurs utilise. Il est important de définir la classe de stockage juste au moment où vous déployez un groupe de serveurs, car cette opération ne peut pas être modifiée après le déploiement. Si vous deviez modifier la classe de stockage après le déploiement, vous auriez besoin d’extraire les données, de supprimer votre groupe de serveurs, de créer un nouveau groupe de serveurs et d’importer les données. Vous pouvez spécifier les classes de stockage à utiliser pour les données, les journaux et les sauvegardes. Par défaut, si vous n’indiquez pas de classes de stockage, les classes de stockage du contrôleur de données sont utilisées.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-data` ou `-scd` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-logs` ou `-scl` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les sauvegardes : dans cette préversion de PostgreSQL Hyperscale avec Azure Arc, il existe deux façons de définir des classes de stockage en fonction des types d’opérations de sauvegarde/restauration que vous souhaitez effectuer. Nous nous efforçons actuellement de simplifier cette expérience. Vous indiquez soit une classe de stockage, soit un montage de revendication de volume. Un montage de revendication de volume est une paire constituée d’une revendication de volume persistant existante (dans le même espace de noms) et du type de volume (et des métadonnées facultatives selon le type de volume), séparés par le signe deux-points. Le volume persistant est monté dans chaque pod pour le groupe de serveurs PostgreSQL.
        - Si vous souhaitez planifier uniquement des restaurations complètes de bases de données, définissez le paramètre `--storage-class-backups` ou `-scb` suivi du nom de la classe de stockage.
        - Si vous envisagez d’effectuer à la fois des restaurations complètes de bases de données et des restaurations dans le temps, définissez le paramètre `--volume-claim-mounts` ou `-vcm` suivi du nom d’une revendication de volume et d’un type de volume.


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

- [Effectuer un scale-out de votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](scale-out-in-postgresql-hyperscale-server-group.md)
- [Configuration de stockage et concepts de stockage Kubernetes](storage-configuration.md)
- [Développement des revendications de volume persistant](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modèle de ressources Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


