---
title: Restaurer une base de données dans SQL Managed Instance avec Azure Arc à une limite antérieure dans le temps
description: Explique comment restaurer une base de données à une limite spécifique dans le temps dans SQL Managed Instance avec Azure Arc.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401092"
---
#  <a name="perform-a-point-in-time-restore"></a>Effectuer une limite de restauration dans le temps

Utilisez la limite de restauration dans le temps (PITR) pour créer une base de données en tant que copie d’une autre base de données datant d’un moment donné dans le passé. Cet article explique comment réaliser une limite de restauration dans le temps d’une base de données dans SQL Managed Instance avec Azure Arc.

La limite de restauration dans le temps peut restaurer une base de données :

- à partir d’une base de données existante ;
- vers une nouvelle base de données sur la même instance gérée SQL avec Azure Arc.

Vous pouvez restaurer une base de données à une limite dans le temps dans le cadre d’un paramètre de rétention préconfiguré.

La limite de restauration dans le temps est un paramètre de niveau instance avec deux propriétés : l’objectif de point de récupération (RPO) et la période de rétention. Le RPO détermine la fréquence des sauvegardes de fichier journal. Le RPO est la durée pendant laquelle il faut s’attendre à une perte de données. Le RPO est exprimé en minutes. La période de rétention détermine la durée (en nombre de jours) pendant laquelle les sauvegardes de base de données doivent être stockées par les services de données avec Azure Arc. La période de rétention s’applique aux fichiers de sauvegarde des sauvegardes complète, différentielle et de fichier journal.  

Actuellement, la limite de restauration dans le temps peut restaurer une base de données :

- à partir d’une base de données existante sur une instance ;
- vers une nouvelle base de données sur la même instance.

## <a name="limitations"></a>Limites

La limite de restauration dans le temps vers SQL Managed Instance avec Azure Arc présente les limitations suivantes :

- La limite de restauration dans le temps de l’ensemble d’une instance gérée SQL avec Azure Arc n’est pas possible. 
- Une instance gérée SQL avec Azure Arc qui est déployée avec une haute disponibilité (préversion) ne prend pas actuellement en charge la limite de restauration dans le temps.
- Vous pouvez uniquement effectuer la restauration vers la même instance gérée SQL avec Azure Arc.
- La limite de restauration dans le temps peut uniquement être effectuée à l’aide d’un fichier YAML.
- Les fichiers de sauvegarde plus anciens qui ont dépassé la période de rétention préconfigurée doivent être nettoyés manuellement.
- Le changement de nom d’une base de données démarre une nouvelle chaîne de sauvegarde dans un nouveau dossier.
- La suppression et la création de différentes bases de données portant le même nom ne sont pas gérées correctement pour le moment.

## <a name="description"></a>Description

Une limite de restauration dans le temps restaure une base de données à une limite spécifique dans le temps. Pour restaurer une base de données à une limite spécifique dans le temps, les services de données avec Azure Arc appliquent les fichiers de sauvegarde dans un ordre spécifique. Par exemple :

1. Sauvegarde complète
2. Sauvegarde différentielle 
3. Une ou plusieurs sauvegardes de fichier journal

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="Restauration dans le temps":::

L’instance gérée SQL avec Azure Arc est dotée d’une capacité intégrée permettant d’effectuer une limite de restauration dans le temps. Chaque fois qu’une nouvelle base de données est créée ou restaurée sur une instance gérée SQL avec Azure Arc, des sauvegardes sont automatiquement effectuées. 

Deux paramètres influencent la capacité de limite de restauration dans le temps :

- Objectif de point de récupération 
- Période de rétention

## <a name="create-a-database-from-a-point-in-time"></a>Créer une base de données à partir d’une limite dans le temps

Voici les étapes à suivre pour restaurer une base de données sur la même instance gérée SQL avec Azure Arc à l’aide de `kubectl` :

1. Créez une tâche pour l’opération de restauration. Créez un fichier .yaml avec les paramètres de restauration.

   Par exemple :

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   Modifiez le fichier YAML ci-dessus :

   - `metadata` > `name` : Nom de la ressource personnalisée de la tâche
   - `metadata` > `namespace` : Espace de noms de l’instance gérée SQL avec Azure Arc
   - `source` > `name` : Nom de l’instance gérée SQL avec Azure Arc
   - `source` > `database` : Nom de la base de données **source** sur l’instance gérée SQL avec Azure Arc à restaurer
   - `restorePoint` : Limite dans le temps à restaurer, date et heure au format « UTC »
   - `destination` > `name` : Nom de l’instance gérée SQL avec Azure Arc
   - `destination` > `database` : Nom de la base de données **de destination** sur l’instance gérée SQL avec Azure Arc


   > [!NOTE] 
   > Le nom des instances gérées SQL avec Azure Arc source et de destination doit être le même.

2. Créez une tâche pour lancer l’opération de limite de restauration dans le temps.

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. Vérifiez l’état de la restauration.

   Exécutez la commande suivante pour vérifier l’état de l’opération de restauration.

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   Remplacez `<namespace>` par l’espace de noms qui héberge l’instance SQL.

Une fois que l’état de la tâche de restauration indique **Terminé**, la nouvelle base de données doit être disponible. 

## <a name="troubleshoot-failed-restore-operations"></a>Résoudre les échecs d’opérations de restauration

Si l’état de la tâche de restauration indique **Échec**, exécutez la commande suivante pour rechercher la cause racine dans les événements.

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

Par exemple :
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>Activer/désactiver les sauvegardes automatiques

Le service de limite de restauration dans le temps (PITR) est activé par défaut avec les paramètres suivants :

- Objectif de point de récupération (RPO) = 300 secondes. Les valeurs acceptées sont 0 ou comprises entre 300 et 600 (en secondes).

Le RPO configure le service pour qu’il effectue des sauvegardes de fichier journal de toutes les bases de données sur l’instance gérée SQL avec Azure Arc toutes les 300 secondes ou 5 minutes par défaut. Cette valeur peut être remplacée par la valeur 0 pour désactiver les sauvegardes en cours ou par une valeur supérieure en secondes, selon l’exigence de RPO requise pour les bases de données sur l’instance SQL. 

Vous pouvez désactiver les sauvegardes automatiques pour une instance spécifique de SQL Managed Instance avec Azure Arc ou modifier les paramètres par défaut. Le service PITR lui-même ne peut pas être désactivé.

Vous pouvez modifier le RPO en changeant la valeur de la propriété `recoveryPointObjectiveInSeconds` comme suit :

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Cette commande ouvre la spec de ressource personnalisée pour SQL Managed Instance avec Azure Arc dans votre éditeur par défaut. Rechercher le paramètre `backup` sous `spec` :

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

Modifiez la valeur de `recoveryPointObjectiveInSeconds` dans l’éditeur et enregistrez les modifications pour que le nouveau paramètre prenne effet. 

> [!NOTE]
> La modification du paramètre RPO entraîne le redémarrage du pod contenant l’instance gérée SQL avec Azure Arc. 

## <a name="monitor-backups"></a>Surveiller les sauvegardes

Les sauvegardes sont stockées sous le dossier `/var/opt/mssql/backups/archived/<dbname>/<datetime>`, où `<dbname>` est le nom de la base de données et `<datetime>` serait un timestamp au format UTC, pour le début de chaque sauvegarde complète. Chaque fois qu’une sauvegarde complète est lancée, un nouveau dossier est créé contenant la sauvegarde complète et toutes les sauvegardes différentielles et de fichier journal ultérieures. La sauvegarde complète la plus récente et ses sauvegardes différentielles et de fichier journal ultérieures sont stockées dans le dossier `/var/opt/mssql/backups/current/<dbname><datetime>`.

### <a name="clean-up"></a>Nettoyer 

Si vous avez besoin de supprimer des sauvegardes plus anciennes, soit pour créer de l’espace, soit parce que vous n’en avez plus besoin, n’importe lequel des dossiers du dossier `/var/opt/mssql/backups/archived/` peut être supprimé. La suppression de dossiers au milieu d’une chronologie peut avoir un impact sur la possibilité d’effectuer une limite de restauration dans le temps pendant cette fenêtre. Supprimez d’abord les dossiers les plus anciens afin de permettre une restauration continue dans le temps. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les fonctionnalités et les capacités d’Azure Arc enabled SQL Managed Instance](managed-instance-features.md)

[Commencer en créant un contrôleur de données](create-data-controller.md)

[Vous avez déjà créé un contrôleur de données ? Créez une instance Azure Arc enabled SQL Managed Instance](create-sql-managed-instance.md)
