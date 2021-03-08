---
title: Haute disponibilité Managed Instance avec Azure Arc
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Découvrez comment déployer Managed Instance avec Azure Arc avec la haute disponibilité.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e04b9b98db26b3b9a024a60c6f82820fe20fcbf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692856"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Haute disponibilité Managed Instance avec Azure Arc

Managed Instance avec Azure Arc est déployé sur Kubernetes en tant qu’application conteneurisée et utilise des constructions Kubernetes, telles que des ensembles avec état et un stockage persistant, pour fournir des mécanismes intégrés de surveillance de l’intégrité, de détection des défaillances et de basculement permettant de maintenir l’intégrité du service. Pour plus de fiabilité, vous pouvez également configurer Managed Instance avec Azure Arc à des fins de déploiement avec des réplicas supplémentaires dans une configuration haute disponibilité. La surveillance, la détection des défaillances et le basculement automatique sont gérés par le contrôleur de données des services de données Arc. Ce service assure toutes les opérations, sans intervention de l’utilisateur, de la configuration du groupe de disponibilité jusqu’à l’ajout de bases de données au groupe de disponibilité et à la coordination du basculement et des mises à niveau, en passant par la configuration des points de terminaison de mise en miroir de bases de données. Ce document explore les deux types de haute disponibilité.

## <a name="built-in-high-availability"></a>Haute disponibilité intégrée 

La haute disponibilité intégrée est fournie par Kubernetes lorsque le stockage persistant distant est configuré et partagé avec des nœuds utilisés par le déploiement du service de données Arc. Dans cette configuration, Kubernetes joue le rôle de l’orchestrateur de cluster. En cas de défaillance de l’instance gérée dans d’un conteneur ou du nœud sous-jacent, l'orchestrateur démarre une autre instance du conteneur et s’attache au même stockage persistant. Ce type est activé par défaut lorsque vous déployez Managed Instance avec Azure Arc.

### <a name="verify-built-in-high-availability"></a>Vérifier la haute disponibilité intégrée

Dans cette section, vous allez vérifier la haute disponibilité intégrée fournie par Kubernetes. Lorsque vous suivez la procédure pour tester cette fonctionnalité, vous supprimez le pod d’une instance gérée existante et vérifiez que Kubernetes récupère à partir de cette action. 

### <a name="prerequisites"></a>Prérequis

- Le cluster Kubernetes doit disposer d’un [stockage distant partagé](https://docs.microsoft.com/en-us/azure/azure-arc/data/storage-configuration#factors-to-consider-when-choosing-your-storage-configuration) 
- Managed Instance avec Azure Arc déployé avec un réplica (par défaut)

1. Affichez les pods. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Supprimez le pod d’instance gérée.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Par exemple

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Affichez les pods pour vérifier que l’instance gérée est en phase de récupération.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Par exemple

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Une fois tous les conteneurs du pod récupérés, vous pouvez vous connecter à l’instance gérée.

## <a name="deploy-with-always-on-availability-groups"></a>Déployer avec des groupes de disponibilité Always On

Pour plus de fiabilité, vous pouvez configurer Managed Instance avec Azure Arc à des fins de déploiement avec des réplicas supplémentaires dans une configuration haute disponibilité. 

Fonctionnalités activées par les groupes de disponibilité :

- En cas de déploiement avec plusieurs réplicas, un seul groupe de disponibilité nommé `containedag` est créé. Par défaut, `containedag` a trois réplicas, y compris un réplica principal. Toutes les opérations CRUD pour le groupe de disponibilité sont gérées en interne, y compris la création du groupe de disponibilité ou la jonction des réplicas au groupe de disponibilité créé. Aucun groupe de disponibilité supplémentaire ne peut être créé dans Managed Instance avec Azure Arc.

- Toutes les bases de données sont automatiquement ajoutées au groupe de disponibilité, y compris toutes les bases de données utilisateur et système telles que `master` et `msdb`. Cette fonctionnalité fournit une vue monosystème sur les réplicas des groupes de disponibilité. Notez les bases de données `containedag_master` et `containedag_msdb` si vous vous connectez directement à l’instance. Les bases de données `containedag_*` représentent `master` et `msdb` au sein du groupe de disponibilité.

- Un point de terminaison externe est automatiquement provisionné pour la connexion aux bases de données au sein du groupe de disponibilité. Ce point de terminaison `<managed_instance_name>-svc-external` joue le rôle de l’écouteur du groupe de disponibilité.

### <a name="deploy"></a>Déployer

Pour déployer une instance gérée avec des groupes de disponibilité, exécutez la commande suivante.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Vérification du statut
Une fois l’instance déployée, exécutez les commandes suivantes pour vérifier l’état de votre instance :

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Exemple de sortie :

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Notez le nombre supplémentaire de `Replicas` et le champ `AGstatus` indiquant l’intégrité du groupe de disponibilité. Si tous les réplicas sont activés et synchronisés, cette valeur correspond à `healthy`. 

### <a name="restore-a-database"></a>Restaurer une base de données 
Des étapes supplémentaires sont nécessaires pour restaurer une base de données dans un groupe de disponibilité. Les étapes suivantes montrent comment restaurer une base de données dans une instance gérée et l’ajouter à un groupe de disponibilité. 

1. Exposez le point de terminaison externe d’instance principale en créant un nouveau service Kubernetes.

    Déterminez le pod qui héberge le réplica principal en vous connectant à l’instance gérée et exécutez :

    ```sql
    SELECT @@SERVERNAME
    ```
    Créez le service Kubernetes sur l’instance principale en exécutant la commande ci-dessous si votre cluster Kubernetes utilise des services nodePort. Remplacez `podName` par le nom du serveur retourné à l’étape précédente, `serviceName` par le nom favori du service Kubernetes créé.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Pour un service LoadBalancer, exécutez la même commande, sauf que le type du service créé est `LoadBalancer`. Par exemple : 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Voici un exemple de cette commande exécutée sur Azure Kubernetes Service, où le pod hébergeant le réplica principal est `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Obtenez l’adresse IP du service Kubernetes créé :

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Restaurez la base de données sur le point de terminaison d’instance principale.

    Ajoutez le fichier de sauvegarde de base de au conteneur d’instance principale.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Exemple

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Restaurez le fichier de sauvegarde de base de données en exécutant la commande ci-dessous.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Exemple

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Ajoutez la base de données au groupe de disponibilité.

    Pour que la base de données soit ajoutée au groupe de disponibilité, elle doit s’exécuter en mode de récupération complète et une sauvegarde de fichier journal doit être effectuée. Exécutez les instructions TSQL ci-dessous pour ajouter la base de données restaurée au groupe de disponibilité.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    L’exemple suivant ajoute une base de données nommée `WideWorldImporters` qui a été restaurée sur l’instance :

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> En guide de bonne pratique, vous devez effectuer un nettoyage en supprimant le service Kubernetes créé ci-dessus en exécutant la commande suivante :
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Limites

Les groupes de disponibilité Managed Instance avec Azure Arc présentent les mêmes limitations [ que les groupes de disponibilité de cluster Big Data. Cliquez ici pour en savoir plus.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Étapes suivantes

Découvrir plus d’informations sur les [fonctionnalités et les capacités de SQL Managed Instance avec Azure Arc](managed-instance-features.md)
