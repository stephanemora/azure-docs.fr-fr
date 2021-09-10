---
title: Restaurer une base de données à une limite dans le temps
description: Explique comment effectuer une opération de limite de restauration dans le temps
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532421"
---
#  <a name="perform-a-point-in-time-restore"></a>Effectuer une limite de restauration dans le temps


SQL Managed Instance avec Azure Arc est doté de nombreuses capacités de type PaaS. L’une de ces capacités est la possibilité de restaurer une base de données jusqu’à une limite dans le temps, dans le cadre des paramètres de rétention préconfigurés. Cet article explique comment réaliser une limite de restauration dans le temps d’une base de données dans SQL Managed Instance avec Azure Arc.

La limite de restauration dans le temps est un paramètre de niveau instance avec deux propriétés : l’objectif de point de récupération (RPO) et la durée de rétention (RT). Le paramètre d’objectif de point de récupération détermine la fréquence à laquelle les sauvegardes de fichier journal sont effectuées. Il s’agit également de la durée pendant laquelle il faut s’attendre à une perte de données. La durée de rétention est la durée pendant laquelle les sauvegardes (complète, différentielle et de fichier journal) sont conservées.  

Actuellement, la limite de restauration dans le temps peut restaurer une base de données :

- à partir d’une base de données existante sur une instance SQL
- vers une nouvelle base de données sur la même instance SQL

### <a name="limitations"></a>Limites

La limite de restauration dans le temps vers SQL Managed Instance avec Azure Arc présente les limites suivantes :

- Vous pouvez uniquement effectuer la restauration vers la même instance gérée SQL avec Azure Arc.
- La limite de restauration dans le temps peut uniquement être effectuée à l’aide d’un fichier YAML. 
- Les fichiers de sauvegarde plus anciens qui ont dépassé la période de rétention préconfigurée doivent être nettoyés manuellement.
- Le changement de nom d’une base de données démarre une nouvelle chaîne de sauvegarde dans un nouveau dossier.
- La suppression et la création de différentes bases de données portant le même nom ne sont pas gérées correctement pour le moment.

### <a name="edit-pitr-settings"></a>Modifier les paramètres PITR

##### <a name="enabledisable-automated-backups"></a>Activer/désactiver les sauvegardes automatiques

Le service de restauration à un instant dans le passé (PITR) est activé par défaut avec les paramètres suivants :

- Objectif de point de récupération (RPO) = 300 secondes. Les valeurs acceptées sont 0 ou comprises entre 300 et 600 (en secondes).

Cela implique que les sauvegardes de fichier journal de toutes les bases de données sur l’instance gérée SQL avec Azure Arc seront effectuées toutes les 300 secondes ou 5 minutes par défaut. Cette valeur peut être remplacée par la valeur 0 pour désactiver les sauvegardes en cours ou par une valeur supérieure en secondes, selon l’exigence de RPO requise pour les bases de données sur l’instance SQL. 

Le service PITR lui-même ne peut pas être désactivé, mais les sauvegardes automatiques pour une instance spécifique de SQL Managed Instance avec Azure Arc peuvent être soit désactivées, soit les paramètres par défaut modifiés.

Le RPO peut être modifié en changeant la valeur de la propriété ```recoveryPointObjectiveInSeconds``` comme suit :

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

Cela doit ouvrir le spec Ressource personnalisée pour SQL Managed Instance avec Azure Arc dans votre éditeur par défaut. Rechercher le paramètre ```backup``` sous ```spec``` :

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

Modifiez la valeur de ```recoveryPointObjectiveInSeconds``` dans l’éditeur et enregistrez les modifications pour que le nouveau paramètre prenne effet. 

> [!NOTE]
> La modification du paramètre RPO entraîne le redémarrage du pod contenant l’instance gérée SQL avec Azure Arc. 

### <a name="restore-a-database-to-a-point-in-time"></a>Restaurer une base de données à une limite dans le temps

Une opération de restauration peut être effectuée sur une instance gérée SQL avec Azure Arc pour réaliser une limite de restauration dans le temps à partir d’une base de données source pendant la période de rétention. 
**(1) Créez le fichier YAML comme ci-dessous dans votre éditeur :**

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- name : chaîne unique pour chaque ressource personnalisée qui est requise pour kubernetes
- namespace : espace de noms dans lequel l’instance gérée SQL avec Azure Arc est en cours d’exécution
- source > name : nom de l’instance gérée SQL avec Azure Arc
- source > database : nom de la base de données source sur l’instance gérée SQL avec Azure Arc
- restorePoint : limite dans le temps pour l’opération de restauration en heure « UTC »
- destination > name : nom de l’instance gérée SQL avec Azure Arc cible sur laquelle effectuer la restauration. Actuellement, seules les restaurations sur les mêmes instances sont prises en charge
- destination > database : nom de la nouvelle base de données sur laquelle la restauration doit être appliquée

**(2) Appliquez le fichier YAML pour créer une tâche pour initier l’opération de restauration.**

Exécutez la commande comme suit pour lancer l’opération de restauration :

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> Le nom de la tâche dans la ressource personnalisée et le nom du fichier n’ont pas besoin d’être identiques.


**Vérifiez l’état de la restauration.**

- L’état de la tâche de restauration est mis à jour environ toutes les 10 secondes et suit le cycle suivant : « En attente » --> « Restauration en cours » --> « Terminé »/« Échec ». 
- Pendant la restauration d’une base de données, l’état reflète « Restauration en cours ».

L’état de la tâche peut être récupéré comme suit :

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>Surveillez vos sauvegardes

Les sauvegardes sont stockées sous le dossier ```/var/opt/mssql/backups/archived/<dbname>/<datetime>```, où ```<dbname>``` est le nom de la base de données et ```<datetime>``` serait un timestamp au format UTC, pour le début de chaque sauvegarde complète. Chaque fois qu’une sauvegarde complète est lancée, un nouveau dossier est créé contenant la sauvegarde complète et toutes les sauvegardes différentielles et de fichier journal ultérieures. La sauvegarde complète la plus récente et ses sauvegardes différentielles et de fichier journal ultérieures sont stockées dans le dossier ```/var/opt/mssql/backups/current/<dbname><datetime>```.


### <a name="clean-up"></a>Nettoyer 

Si vous avez besoin de supprimer des sauvegardes plus anciennes, soit pour créer de l’espace, soit parce que vous n’en avez plus besoin, n’importe lequel des dossiers du dossier ```/var/opt/mssql/backups/archived/``` peut être supprimé. La suppression de dossiers au milieu d’une chronologie peut avoir un impact sur la possibilité d’effectuer une limite de restauration dans le temps pendant cette fenêtre. Il est recommandé de supprimer d’abord les dossiers les plus anciens afin de permettre une restauration continue dans le temps. 


