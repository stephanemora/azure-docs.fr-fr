---
title: Créer un groupe de serveurs PostgreSQL Hyperscale compatible avec Azure Arc
description: Créer un groupe de serveurs PostgreSQL Hyperscale compatible avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687938"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Créer un groupe de serveurs PostgreSQL Hyperscale compatible avec Azure Arc

Ce document décrit les étapes de création d’un groupe de serveurs PostgreSQL Hyperscale sur Azure Arc.

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


## <a name="login-to-the-azure-arc-data-controller"></a>Se connecter au contrôleur de données Azure Arc

Avant de pouvoir créer une instance, vous devez d’abord vous connecter au contrôleur de données Azure Arc. Si vous êtes déjà connecté au contrôleur de données, vous pouvez ignorer cette étape.

```console
azdata login
```

Vous serez ensuite invité à entrer le nom d’utilisateur, le mot de passe et l’espace de noms système.  

> Si vous avez utilisé le script pour créer le contrôleur de données, votre espace de noms doit être **arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Étape préliminaire et temporaire pour les utilisateurs OpenShift uniquement
Implémentez cette étape avant de passer à l’étape suivante. Pour déployer le groupe de serveurs PostgreSQL Hyperscale sur Red Hat OpenShift dans un projet autre que celui par défaut, vous devez exécuter les commandes suivantes sur votre cluster pour mettre à jour les contraintes de sécurité. Cette commande accorde les privilèges nécessaires aux comptes de service qui exécuteront votre groupe de serveurs PostgreSQL Hyperscale. La contrainte de contexte de sécurité (SCC) arc-data-scc est celle que vous avez ajoutée lors du déploiement du contrôleur de données Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-Group-Name est le nom du groupe de serveurs que vous allez créer à l’étape suivante.**

Pour plus d’informations sur SCC dans OpenShift, consultez la documentation [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Vous pouvez maintenant implémenter l’étape suivante.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Créer un groupe de serveurs PostgreSQL Hyperscale compatible avec Azure Arc

Pour créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc sur votre contrôleur de données Arc, vous allez utiliser la commande `azdata arc postgres server create` à laquelle vous allez passer plusieurs paramètres.

Pour plus d’informations sur tous les paramètres que vous pouvez définir au moment de la création, passez en revue la sortie de la commande :
```console
azdata arc postgres server create --help
```

Les principaux paramètres à prendre en compte sont les suivants :
- **Nom du groupe de serveurs** que vous voulez déployer. Indiquez `--name` ou `-n` suivi d’un nom dont la longueur ne doit pas dépasser 11 caractères.

- **Version du moteur PostgreSQL** que vous souhaitez déployer : par défaut, il s’agit de la version 12. Pour déployer la version 12, vous pouvez omettre ce paramètre ou passer l’un des paramètres suivants : `--engine-version 12` ou `-ev 12`. Pour déployer la version 11, indiquez `--engine-version 11` ou `-ev 11`.

- **Nombre de nœuds Worker** que vous souhaitez déployer pour effectuer un scale-out et potentiellement obtenir de meilleures performances. Avant de continuer, lisez les [concepts relatifs à Postgres Hyperscale](concepts-distributed-postgres-hyperscale.md). Pour indiquer le nombre de nœuds Worker à déployer, utilisez le paramètre `--workers` ou `-w` suivi d’un entier supérieur ou égal à 2. Par exemple, si vous souhaitez déployer un groupe de serveurs avec 2 nœuds Worker, indiquez `--workers 2` ou `-w 2`. Cette opération crée trois pods, un pour le nœud coordinateur/l’instance et deux pour les nœuds Worker/instances (un pour chacun des Workers).

- **Les classes de stockage** que vous souhaitez que votre groupe de serveurs utilise. Il est important de définir la classe de stockage juste au moment où vous déployez un groupe de serveurs, car cette opération ne peut pas être modifiée après le déploiement. Si vous deviez modifier la classe de stockage après le déploiement, vous auriez besoin d’extraire les données, de supprimer votre groupe de serveurs, de créer un nouveau groupe de serveurs et d’importer les données. Vous pouvez spécifier les classes de stockage à utiliser pour les données, les journaux et les sauvegardes. Par défaut, si vous n’indiquez pas de classes de stockage, les classes de stockage du contrôleur de données sont utilisées.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-data` ou `-scd` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les données, indiquez le paramètre `--storage-class-logs` ou `-scl` suivi du nom de la classe de stockage.
    - Pour définir la classe de stockage pour les sauvegardes : dans cette préversion de PostgreSQL Hyperscale avec Azure Arc, il existe deux façons de définir des classes de stockage en fonction des types d’opérations de sauvegarde/restauration que vous souhaitez effectuer. Nous nous efforçons actuellement de simplifier cette expérience. Vous indiquez soit une classe de stockage, soit un montage de revendication de volume. Un montage de revendication de volume est une paire constituée d’une revendication de volume persistant existante (dans le même espace de noms) et du type de volume (et des métadonnées facultatives selon le type de volume), séparés par le signe deux-points. Le volume persistant est monté dans chaque pod pour le groupe de serveurs PostgreSQL.
        - Si vous souhaitez planifier uniquement des restaurations complètes de bases de données, définissez le paramètre `--storage-class-backups` ou `-scb` suivi du nom de la classe de stockage.
        - Si vous envisagez d’effectuer à la fois des restaurations complètes de bases de données et des restaurations dans le temps, définissez le paramètre `--volume-claim-mounts` ou `-vcm` suivi du nom d’une revendication de volume et d’un type de volume.

Notez que lorsque vous exécutez la commande create, vous êtes invité à entrer le mot de passe de l’utilisateur administratif `postgres` par défaut. Le nom de cet utilisateur ne peut pas être modifié dans cette préversion. Vous pouvez ignorer l’invite interactive en définissant la variable d’environnement de session `AZDATA_PASSWORD` avant d’exécuter la commande create.

### <a name="examples"></a>Exemples

**Pour déployer un groupe de serveurs de Postgres version 12 nommé postgres01 avec 2 nœuds Worker qui utilise les mêmes classes de stockage que le contrôleur de données, exécutez la commande suivante :**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Pour déployer un groupe de serveurs de Postgres version 12 nommé postgres01 avec 2 nœuds Worker qui utilise les mêmes classes de stockage que le contrôleur de données pour les données et les journaux, mais la classe de stockage spécifique pour effectuer à la fois les restaurations complètes et les restaurations dans le temps, procédez comme suit :**

 Cet exemple suppose que votre groupe de serveurs est hébergé dans un cluster Azure Kubernetes Service (AKS). Cet exemple utilise azurefile-premium comme nom de classe de stockage. Vous pouvez ajuster l’exemple ci-dessous en fonction des caractéristiques de votre environnement. Notez que **accessModes ReadWriteMany est requis** pour cette configuration.  

Tout d’abord, créez un fichier YAML qui contient la description ci-dessous du PVC de sauvegarde et nommez-le CreateBackupPVC.yml par exemple :
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Créez ensuite un PVC à l’aide de la définition stockée dans le fichier YAML :

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Ensuite, créez le groupe de serveurs :

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Lisez les [limitations actuelles relatives à la sauvegarde/restauration](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Si vous avez déployé le contrôleur de données à l’aide des variables d’environnement de session `AZDATA_USERNAME` et `AZDATA_PASSWORD` dans la même session de terminal, les valeurs d’`AZDATA_PASSWORD` seront également utilisées pour déployer le groupe de serveurs PostgreSQL Hyperscale. Si vous préférez utiliser un autre mot de passe, vous pouvez (1) mettre à jour la valeur d’`AZDATA_PASSWORD` ou (2) supprimer la variable d’environnement `AZDATA_PASSWORD` ou (3) supprimer sa valeur pour être invité à entrer un mot de passe de manière interactive lorsque vous créez un groupe de serveurs.
> - La création d’un groupe de serveurs PostgreSQL Hyperscale n’inscrira pas immédiatement les ressources dans Azure. Dans le cadre du processus de chargement de l’[inventaire des ressources](upload-metrics-and-logs-to-azure-monitor.md) ou des [données d’utilisation](view-billing-data-in-azure.md) à Azure, les ressources seront créées dans Azure et vous pourrez voir vos ressources dans le Portail Azure.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Répertorier les groupes de serveurs PostgreSQL Hyperscale déployés dans votre contrôleur de données Arc

Pour répertorier les groupes de serveurs PostgreSQL Hyperscale déployés dans votre contrôleur de données Arc, exécutez la commande suivante :

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Obtenir les points de terminaison pour vous connecter à votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Pour afficher les points de terminaison d’un groupe de serveurs PostgreSQL, exécutez la commande suivante :

```console
azdata arc postgres endpoint list -n <server group name>
```
Par exemple :
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Vous pouvez utiliser le point de terminaison d’instance PostgreSQL pour vous connecter au groupe de serveurs PostgreSQL Hyperscale à partir de votre outil favori : [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin, etc. Dans ce cas, vous vous connectez au nœud coordinateur/à l’instance qui prend en charge le routage de la requête vers les nœuds de travail/instances appropriés si vous avez créé des tables distribuées. Pour plus d’informations, consultez les [concepts en lien avec PostgreSQL Hyperscale avec Azure Arc](concepts-distributed-postgres-hyperscale.md).

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Remarque spéciale sur les déploiements de machines virtuelles Azure

Lorsque vous utilisez une machine virtuelle Azure, l’adresse IP du point de terminaison n’affiche pas l’adresse IP _publique_. Utilisez la commande suivante pour localiser l’adresse IP publique :

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Vous pouvez ensuite combiner l’adresse IP publique avec le port pour établir votre connexion.

Vous devrez peut-être également exposer le port du groupe de serveurs PostgreSQL Hyperscale à l’aide de la passerelle de sécurité réseau (NSG). Pour autoriser le trafic via la passerelle de sécurité réseau (NSG), vous devez ajouter une règle que vous pouvez effectuer à l’aide de la commande suivante :

Pour définir une règle, vous devez connaître le nom de votre groupe de sécurité réseau. Vous déterminez le groupe de sécurité réseau à l’aide de la commande ci-dessous :

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Une fois que vous avez le nom du groupe de sécurité réseau, vous pouvez ajouter une règle de pare-feu à l’aide de la commande suivante. Les exemples de valeurs ci-dessous créent une règle NSG pour le port 30655 et autorisent la connexion à partir de **n’importe quelle adresse IP source** .  Il ne s’agit pas d'une meilleure pratique de sécurité.  Vous pouvez mieux verrouiller les choses en spécifiant une valeur-source-adresse-préfixes spécifique à votre adresse IP du client ou une plage d’adresses IP qui couvre les adresses IP de votre équipe ou de votre organisation.

Remplacez la valeur du paramètre --destination-port-ranges ci-dessous par le numéro de port que vous avez obtenu à partir de la commande « azdata arc postgres Server List » ci-dessus.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Se connecter avec Azure Data Studio

Ouvrez Azure Data Studio et connectez-vous à votre instance avec l’adresse IP et le numéro de port du point de terminaison externe, ainsi que le mot de passe que vous avez spécifié au moment de la création de l’instance.  Si PostgreSQL n’est pas disponible dans la liste déroulante *Type de connexion*, vous pouvez installer l’extension PostgreSQL en recherchant PostgreSQL sous l’onglet Extensions.

> [!NOTE]
> Vous devrez cliquer sur le bouton [Avancé] dans le panneau de connexion pour entrer le numéro de port.

N’oubliez pas que si vous utilisez une machine virtuelle Azure, vous aurez besoin de l’_adresse IP_ publique qui est accessible via la commande suivante :

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Se connecter avec psql

Pour accéder à votre groupe de serveurs PostgreSQL Hyperscale, transmettez le point de terminaison externe du groupe de serveurs PostgreSQL Hyperscale que vous avez récupéré ci-dessus :

Vous pouvez maintenant vous connecter à psql :

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Étapes suivantes

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
