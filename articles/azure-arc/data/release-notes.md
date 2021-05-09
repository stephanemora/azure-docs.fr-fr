---
title: Services de données activés par Azure Arc – Notes de publication
description: Dernières notes de publication
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 5931b28553b7a6030dc8c7b0adb2c42111ce6751
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989411"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notes de publication – Services de données activés par Azure Arc (préversion)

Cet article met en évidence les capacités, fonctionnalités et améliorations récemment mises en production ou améliorées pour les services de données avec Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Mars 2021

La version initiale de mars 2021 a été publiée le 5 avril 2021, et les dernières étapes de sa publication ont été achevées le 9 avril 2021.

Pour en plus savoir plus sur les limitations de cette version, consultez [Problèmes connus - Services de données avec Azure Arc (préversion)](known-issues.md)

Numéro de version d'Azure Data CLI (`azdata`) : 20.3.2. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Contrôleur de données

- Déployez le contrôleur de données des services de données avec Azure Arc en mode de connexion directe à partir du portail. Commencez par [Déployer le contrôleur de données - Mode de connexion directe - Prérequis](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled PostgreSQL Hyperscale

Les deux définitions de ressources personnalisées (CRD) relatives à PostgreSQL ont été regroupées en une. Consultez le tableau suivant.

|Libérer |CRD |
|-----|-----|
|Février 2021 et versions antérieures| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|À compter de mars 2021 | postgresqls.arcdata.microsoft.com

Vous supprimerez les CRD précédentes au fur et à mesure que vous nettoierez les installations antérieures. Consultez [Nettoyage des installations précédentes](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Vous pouvez maintenant créer une SQL Managed Instance à partir du portail Azure en mode de connexion directe.

- Vous pouvez maintenant restaurer une base de données sur SQL Managed Instance avec 3 réplicas, et celle-ci sera automatiquement ajoutée au groupe de disponibilité. 

- Vous pouvez maintenant vous connecter à un point de terminaison en lecture seule secondaire sur des SQL Managed Instances déployées avec 3 réplicas. Utilisez `azdata arc sql endpoint list` pour voir le point de terminaison de connexion en lecture seule secondaire.

### <a name="known-issues"></a>Problèmes connus

- En mode de connexion directe, le chargement de l'utilisation, des métriques et des journaux à l'aide de `azdata arc dc upload` est actuellement bloqué. Les données d'utilisation sont automatiquement chargées. Le chargement du contrôleur de données créé en mode de connexion indirecte doit continuer à fonctionner.
- Le déploiement d'un contrôleur de données en mode de connexion directe ne peut être effectué qu'à partir du portail Azure, et il n'est pas disponible à partir d'outils clients tels qu'azdata, Azure Data Studio ou kubectl.
- Le déploiement de SQL Managed Instance avec Azure Arc en mode de connexion directe ne peut être effectué qu'à partir du portail Azure, et il n'est pas disponible à partir d'outils tels qu'azdata, Azure Data Studio ou kubectl.
- Le déploiement de PostgeSQL Hyperscale avec Azure Arc en mode de connexion directe n'est pas disponible actuellement.
- Le chargement automatique des données d'utilisation en mode de connexion directe échoue si vous utilisez un proxy via `–proxy-cert <path-t-cert-file>`.
- SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc ne sont pas certifiés GB18030.

## <a name="february-2021"></a>Février 2021

### <a name="new-capabilities-and-features"></a>Nouvelles capacités et fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.3.1. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Les mises à jour supplémentaires incluent :

- SQL Managed Instance avec Azure Arc
   - Haute disponibilité avec des groupes de disponibilité Always On

- PostgreSQL Hyperscale avec Azure Arc  Azure Data Studio : 
   - La page vue d’ensemble affiche maintenant l’état du groupe de serveurs détaillé par nœud
   - Une nouvelle page de propriétés est désormais disponible pour afficher des détails supplémentaires sur le groupe de serveurs
   - Configurer les paramètres du moteur Postgres à partir de la page **Paramètres du nœud**

Pour les problèmes associés à cette mise en production, consultez [Problèmes connus – Services de données avec Azure Arc (préversion)](known-issues.md)

## <a name="january-2021"></a>Janvier 2021

### <a name="new-capabilities-and-features"></a>Nouvelles capacités et fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.3.0. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Les mises à jour supplémentaires incluent :
- Portail localisé disponible dans 17 nouvelles langues
- Modifications mineures apportées aux fichiers .yaml Kube natifs
- Nouvelles versions de Grafana et Kibana
- Les problèmes affectant les environnements Python lors de l’utilisation d’azdata dans des notebooks dans Azure Data Studio ont été résolus
- L’extension pg_audit est désormais disponible pour PostgreSQL Hyperscale
- Aucun ID de sauvegarde n’est nécessaire pour effectuer une restauration complète d’une base de données PostgreSQL Hyperscale
- L’état (état d’intégrité) est indiqué pour chacune des instances PostgreSQL qui constituent un groupe de serveurs

   Dans les versions antérieures, l’état était agrégé au niveau du groupe de serveurs et n’était pas répertorié au niveau du nœud PostgreSQL.

- Les déploiements PostgreSQL respectent désormais les paramètres de taille de volume indiqués dans les commandes create
- Les paramètres de version du moteur sont désormais respectés lors de la modification d’un groupe de serveurs
- La convention d’affectation de noms des pods pour Azure Arc prenant en charge l’hyperscale PostgreSQL a changé

    Il se présente désormais sous la forme : `ServergroupName{c, w}-n`. Par exemple, un groupe de serveurs avec trois nœuds, un nœud coordinateur et deux nœuds Worker sont représentés comme suit :
   - `Postgres01c-0` (nœud coordinateur)
   - `Postgres01w-0` (nœud Worker)
   - `Postgres01w-1` (nœud Worker)

## <a name="december-2020"></a>Décembre 2020

### <a name="new-capabilities--features"></a>Nouvelles fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.2.5. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Affichez les points de terminaison pour SQL Managed Instance et l’hyperscale PostgreSQL à l’aide d’Azure Data CLI (`azdata`) avec des commandes `azdata arc sql endpoint list` et `azdata arc postgres endpoint list` .

Modifiez les demandes et les limites de ressources SQL Managed Instance (processeur et mémoire) à l’aide d’Azure Data Studio.

L’hyperscale PostgreSQL avec Azure ARC prend désormais en charge la restauration à un point dans le temps en plus de la restauration de sauvegarde complète pour les versions 11 et 12 de PostgreSQL. La fonction de restauration dans le temps vous permet d’indiquer une date et une heure spécifiques de restauration.

La convention d’affectation de noms des pods pour Azure Arc prenant en charge l’hyperscale PostgreSQL a changé. Il se présente désormais sous la forme : ServergroupName{r, s}-_n_. Par exemple, un groupe de serveurs avec trois nœuds, un nœud coordinateur et deux nœuds Worker sont représentés comme suit :
- `postgres02r-0` (nœud coordinateur)
- `postgres02s-0` (nœud Worker)
- `postgres02s-1` (nœud Worker)

### <a name="breaking-change"></a>Modification avec rupture

#### <a name="new-resource-provider"></a>Nouveau fournisseur de ressources

Cette version inaugure un [fournisseur de ressources](../../azure-resource-manager/management/azure-services-resource-providers.md) mis à jour appelé `Microsoft.AzureArcData`. Avant de pouvoir utiliser cette fonctionnalité, vous devez inscrire ce fournisseur de ressources. 

Pour inscrire le fournisseur de ressources : 

1. Dans le portail Azure, sélectionnez **Abonnements** 
2. Choisir votre abonnement
3. Sous **Paramètres**, sélectionnez **Fournisseurs de ressources** 
4. Recherchez `Microsoft.AzureArcData` et sélectionnez **Inscrire** 

Vous pouvez consulter les étapes détaillées dans [Types de ressources et fournisseurs Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Cette modification supprime également toutes les ressources Azure existantes que vous avez téléchargées sur le Portail Azure. Pour pouvoir utiliser le fournisseur de ressources, vous devez mettre à jour le contrôleur de données et utiliser la dernière `azdata` CLI.  

### <a name="platform-release-notes"></a>Notes de publication de la plateforme

#### <a name="direct-connectivity-mode"></a>Mode de connectivité directe

Cette version introduit le mode de connectivité directe. Le mode de connectivité directe permet au contrôleur de données de télécharger automatiquement les informations d’utilisation dans Azure. Dans le cadre du chargement de l’utilisation, la ressource de contrôleur de données Arc est créée automatiquement dans le portail, si elle n’est pas déjà créée via le chargement de `azdata` .  

Vous pouvez spécifier une connectivité directe quand vous créez le contrôleur de données. L’exemple suivant crée un contrôleur de données avec `azdata arc dc create` nommé `arc` à l’aide du mode de connectivité directe (`connectivity-mode direct`). Avant d’exécuter l’exemple, remplacez `<subscription id>` par votre ID d’abonnement.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Problèmes connus

- Sur Azure Kubernetes service (AKS), la version 1.19. x de Kubernetes n’est pas prise en charge.
- Sur Kubernetes 1.19, `containerd` n’est pas pris en charge.
- La ressource de contrôleur de données dans Azure est actuellement une ressource Azure. Toutes les mises à jour telles que les suppressions ne sont pas propagées vers le cluster kubernetes.
- Les noms d’instance ne peuvent pas dépasser 13 caractères
- Aucune mise à niveau sur place du contrôleur de données ou des instances de base de données Azure Arc.
- Les images conteneurs de services de données activés par Arc ne sont pas signées.  Il se peut que vous deviez configurer vos nœuds Kubernetes pour permettre l’extraction d’images de conteneur non signées.  Par exemple, si vous utilisez Docker en tant que runtime de conteneurs, vous pouvez définir la variable d’environnement DOCKER_CONTENT_TRUST=0 et redémarrer.  D’autres runtimes de conteneurs offrent des options similaires, par exemple dans [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Impossible de créer des instances gérées SQL activées par Azure Arc, ou des groupes de serveurs PostgreSQL Hyperscale à partir du portail Azure.
- Pour le moment, si vous utilisez NFS, vous devez définir `allowRunAsRoot` sur `true` dans votre fichier de profil de déploiement avant de créer le contrôleur de données Azure Arc.
- Authentification de connexion SQL et PostgreSQL uniquement.  Aucune prise en charge d’Azure Active Directory ou d’Active Directory.
- La création d’un contrôleur de données sur OpenShift impose des contraintes de sécurité peu exigeantes.  Pour plus d'informations, reportez-vous à la documentation.
- Si vous utilisez Azure Kubernetes Service (AKS) sur Azure Stack Hub avec un contrôleur de données Azure Arc et des instances de base de données, la mise à niveau vers une version plus récente de Kubernetes n’est pas prise en charge. Avant de mettre à niveau le cluster Kubernetes, désinstallez le contrôleur de données Azure Arc et toutes les instances de base de données.
- Pour les clusters AKS qui s'étendent sur [plusieurs zones de disponibilité](../../aks/availability-zones.md), les services de données avec Azure Arc ne sont actuellement pas pris en charge. Pour contourner ce problème, lorsque vous créez le cluster AKS sur le portail Azure, si vous sélectionnez une région dans laquelle des zones sont disponibles, effacez toutes les zones du contrôle de sélection. Consultez le graphique suivant :

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Décochez les cases des différentes zones pour n'en spécifier aucune.":::

## <a name="october-2020"></a>Octobre 2020 

Numéro de version d’Azure Data CLI (`azdata`) : 20.2.3. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Changements cassants

Cette version introduit les changements cassants suivants : 

* Dans la définition de ressource personnalisée PostgreSQL, le terme `shards` est renommé en `workers`. Ce terme (`workers`) correspond au nom du paramètre de ligne de commande.

* `azdata arc postgres server delete` demande une confirmation avant de supprimer une instance postgres.  Utilisez `--force` pour ignorer l’invite.

### <a name="additional-changes"></a>Modifications supplémentaires

* Un nouveau paramètre facultatif a été ajouté à `azdata arc postgres server create` appelé `--volume-claim mounts`. La valeur est une liste de montages de revendications de volume séparés par des virgules. Un montage de revendication de volume est une paire de type de volume et de nom PVC. Le seul type de volume actuellement pris en charge est `backup`.  Dans PostgreSQL, lorsque le type de volume est `backup`, le PVC est monté sur `/mnt/db-backups`.  Cela permet de partager des sauvegardes entre instances PostgresSQL afin que la sauvegarde d’une instance PostgresSQL puisse être restaurée dans une autre instance.

* Nouveaux noms courts pour les définitions de ressources personnalisées PostgresSQL : 
  * `pg11` 
  * `pg12`
* Le chargement de la télémétrie fournit à l’utilisateur l’un des éléments suivants :
   * Nombre de points chargés sur Azure ou 
   * Si aucune donnée n’a été chargée sur Azure, une invite pour essayer à nouveau.
* `azdata arc dc debug copy-logs` lit maintenant aussi dans le dossier `/var/opt/controller/log` et collecte les journaux du moteur PostgreSQL sur Linux.
*   Affichage d’un indicateur de travail lors de la création et de la restauration de la sauvegarde avec PostgreSQL Hyperscale.
* `azdata arc postrgres backup list` comprend désormais des informations sur la taille de la sauvegarde.
* La propriété Nom de l’administrateur SQL Managed Instance a été ajoutée à la colonne de droite du panneau Vue d’ensemble dans le portail Azure.
* Azure Data Studio prend en charge la configuration du nombre de nœuds Worker, de vCore et des paramètres de mémoire pour PostgreSQL Hyperscale. 
* La préversion prend en charge la sauvegarde/restauration pour les versions 11 et 12 de Postgres.

## <a name="september-2020"></a>Septembre 2020

Les services de données activés par Azure Arc sont mis en production pour la préversion publique. Les services de données activés par Arc permettent de gérer les services de données en tout lieu.

- Instance managée SQL
- PostgreSQL Hyperscale

Pour obtenir des instructions, consultez [Présentation des services de données avec Azure Arc](overview.md)

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.

- [Installer les outils clients](install-client-tools.md)
- [Créer le contrôleur de données Azure Arc](create-data-controller.md) (nécessite l'installation préalable des outils clients)
- [Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Fournisseurs de ressources pour les services Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
