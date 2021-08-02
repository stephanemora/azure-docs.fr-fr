---
title: Créer un PostgreSQL Hyperscale activé par Azure Arc à l’aide d’Azure Data Studio
description: Créer un PostgreSQL Hyperscale activé par Azure Arc à l’aide d’Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: afcd4eb8327ff806e76b635d8be3715d93b15ed6
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111407702"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Créer un PostgreSQL Hyperscale activé par Azure Arc à l’aide d’Azure Data Studio

Ce document vous guide tout au long des étapes d’utilisation d’Azure Data Studio pour configurer des groupes de serveurs PostgreSQL Hyperscale activés pour Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Se connecter au contrôleur de données Azure Arc

Avant de pouvoir créer une instance, connectez-vous au contrôleur de données Azure Arc si vous ne l’avez pas encore fait.

```console
azdata login
```

Vous êtes ensuite invité à entrer l’espace de noms dans lequel le contrôleur de données est déployé, le nom d’utilisateur et le mot de passe pour se connecter au contrôleur.

> Si vous devez valider l’espace de noms, vous pouvez exécuter ```kubectl get pods -A``` pour obtenir une liste de tous les espaces de noms sur le cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Étape préliminaire et temporaire pour les utilisateurs OpenShift uniquement

Implémentez cette étape avant de passer à l’étape suivante. Pour déployer le groupe de serveurs PostgreSQL Hyperscale sur Red Hat OpenShift dans un projet autre que celui par défaut, vous devez exécuter les commandes suivantes sur votre cluster pour mettre à jour les contraintes de sécurité. Cette commande accorde les privilèges nécessaires aux comptes de service qui exécuteront votre groupe de serveurs PostgreSQL Hyperscale. La contrainte de contexte de sécurité (SCC) **_arc-data-scc_** est celle que vous avez ajoutée lors du déploiement du contrôleur de données Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** est le nom du groupe de serveurs que vous allez déployer à l’étape suivante._
   
Pour plus d’informations sur SCC dans OpenShift, consultez la documentation [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Vous pouvez maintenant implémenter l’étape suivante.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Créer un groupe de serveurs PostgreSQL Hyperscale compatible avec Azure Arc

1. Lancer Azure Data Studio
1. Sous l’onglet Connexions, cliquez sur les trois points situés en haut à gauche, puis choisissez Nouveau déploiement
1. Dans les options de déploiement, sélectionnez **Groupe de serveurs PostgreSQL Hyperscale - Azure Arc**
    >[!NOTE]
    > Vous pouvez être invité à installer l’outil [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ici s’il n’est pas actuellement installé.
1. Acceptez la déclaration de confidentialité et les termes du contrat de licence, puis cliquez sur **Sélectionner** en bas
1. Dans le panneau Déployer le groupe de serveurs PostgreSQL Hyperscale - Azure Arc, entrez les informations suivantes :
   - Entrez un nom pour le groupe de serveur
   - Nombre de nœuds Worker
   - Entrez et confirmez un mot de passe pour l'utilisateur administrateur de _postgres_ du groupe de serveurs
   - Sélectionnez la classe de stockage appropriée pour les données
   - Sélectionnez la classe de stockage appropriée pour les journaux
   - Sélectionnez la classe de stockage appropriée pour les sauvegardes
   - Sélectionnez le nombre de nœuds Worker à approvisionner
1. Cliquez sur le bouton **Déployer**

Cela démarre la création du groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc sur le contrôleur de données.

Votre création ne devrait prendre que quelques minutes.

### <a name="important-parameters-you-should-consider"></a>Paramètres importants à prendre en compte :

- **Nombre de nœuds Worker** que vous souhaitez déployer pour effectuer un scale-out et potentiellement obtenir de meilleures performances. Avant de continuer, lisez les [concepts relatifs à Postgres Hyperscale](concepts-distributed-postgres-hyperscale.md). Le tableau ci-dessous indique la plage des valeurs prises en charge et le type de déploiement Postgres obtenu avec ces valeurs. Par exemple, si vous souhaitez déployer un groupe de serveurs avec 2 nœuds Worker, indiquez 2. Cette opération crée trois pods, un pour le nœud coordinateur/l’instance et deux pour les nœuds Worker/instances (un pour chacun des Workers).



|Vous devez avoir   |Forme du groupe de serveurs que vous allez déployer   |Nombre de nœuds Worker à indiquer   |Notes   |
|---|---|---|---|
|Forme de Postgres avec scale-out pour répondre aux besoins de scalabilité de vos applications.   |3 instances Postgres ou plus, 1 est coordinateur, n sont des workers avec n >=2.   |n, avec n>=2.   |L’extension Citus qui fournit la fonctionnalité Hyperscale est chargée.   |
|Forme de base de Postgres Hyperscale permettant la validation fonctionnelle de votre application à un coût minimal. Non valide pour la validation des performances et de la scalabilité. En fonction de vos besoins, vous devez utiliser l’un des types de déploiement décrit ci-dessus.   |1 instance Postgres qui est à la fois coordinateur et Worker.   |0 et ajoutez Citus à la liste des extensions à charger.   |L’extension Citus qui fournit la fonctionnalité Hyperscale est chargée.   |
|Instance simple de Postgres prête pour un scale-out quand vous en avez besoin.   |1 instance Postgres. La sémantique pour le coordinateur et le Worker n’est pas encore déterminée. Pour effectuer un scale-out après le déploiement, modifiez la configuration, augmentez le nombre de nœuds Worker et distribuez les données.   |0   |L’extension Citus qui fournit la fonctionnalité Hyperscale est présente dans votre déploiement, mais elle n’est pas encore chargée.   |
|   |   |   |   |

Si vous indiquez 1 Worker, le déploiement fonctionne mais nous vous déconseillons de l’utiliser. Ce déploiement vous apporte peu de valeur. Cette configuration vous permet d’obtenir 2 instances de Postgres : 1 coordinateur et 1 Worker. Avec cette configuration, vous n’effectuez pas le scale-out des données, car vous déployez un seul Worker. Par conséquent, le niveau de performances et de scalabilité n’augmente pas. La prise en charge de ce déploiement sera supprimée dans une version ultérieure.

- **Les classes de stockage** que vous souhaitez que votre groupe de serveurs utilise. Il est important de définir la classe de stockage juste au moment où vous déployez un groupe de serveurs, car cette opération ne peut pas être modifiée après le déploiement. Si vous deviez modifier la classe de stockage après le déploiement, vous auriez besoin d’extraire les données, de supprimer votre groupe de serveurs, de créer un nouveau groupe de serveurs et d’importer les données. Vous pouvez spécifier les classes de stockage à utiliser pour les données, les journaux et les sauvegardes. Par défaut, si vous n’indiquez pas de classes de stockage, les classes de stockage du contrôleur de données sont utilisées.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-data` ou `-scd` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-logs` ou `-scl` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les sauvegardes : dans cette préversion de PostgreSQL Hyperscale avec Azure Arc, il existe deux façons de définir des classes de stockage en fonction des types d’opérations de sauvegarde/restauration que vous souhaitez effectuer. Nous nous efforçons actuellement de simplifier cette expérience. Vous indiquez soit une classe de stockage, soit un montage de revendication de volume. Un montage de revendication de volume est une paire constituée d’une revendication de volume persistant existante (dans le même espace de noms) et du type de volume (et des métadonnées facultatives selon le type de volume), séparés par le signe deux-points. Le volume persistant est monté dans chaque pod pour le groupe de serveurs PostgreSQL.
        - Si vous souhaitez planifier uniquement des restaurations complètes de bases de données, définissez le paramètre `--storage-class-backups` ou `-scb` suivi du nom de la classe de stockage.
        - Si vous envisagez d’effectuer à la fois des restaurations complètes de bases de données et des restaurations dans le temps, définissez le paramètre `--volume-claim-mounts` ou `-vcm` suivi du nom d’une revendication de volume et d’un type de volume.


## <a name="next-steps"></a>Étapes suivantes
- [Gérer votre groupe de serveurs à l’aide d’Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Surveiller votre groupe de serveurs](monitor-grafana-kibana.md)
- Lisez les concepts et les guides pratiques d’Azure Database pour PostgreSQL Hyperscale pour distribuer vos données sur plusieurs nœuds PostgreSQL Hyperscale et tirer parti de toute la puissance d’Azure Database pour Postgres Hyperscale. :
    * [Nœuds et tables](../../postgresql/concepts-hyperscale-nodes.md)
    * [Déterminer le type d’application](../../postgresql/concepts-hyperscale-app-type.md)
    * [Choisir une colonne de distribution](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalisation de table](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuer et modifier des tables](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Concevoir une base de données multilocataire](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Concevoir un tableau de bord d’analytique en temps réel](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Dans les documents ci-dessus, ignorez les sections **Se connecter au portail Azure** et **Créer un serveur Azure Database pour PostgreSQL - Hyperscale (Citus)** . Implémentez les étapes restantes dans votre déploiement Azure Arc. Ces sections sont spécifiques à Azure Database pour PostgreSQL Hyperscale (Citus) proposé en tant que service PaaS dans le cloud Azure, mais les autres parties des documents s’appliquent directement à votre PostgreSQL Hyperscale activé pour Azure Arc.

- [Effectuer un scale-out de votre groupe de serveurs Azure Database pour PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md)
- [Configuration de stockage et concepts de stockage Kubernetes](storage-configuration.md)
- [Modèle de ressources Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

