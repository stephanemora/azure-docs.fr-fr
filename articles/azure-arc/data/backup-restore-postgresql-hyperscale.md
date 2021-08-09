---
title: Sauvegarde et restauration pour les groupes de serveurs Azure Database pour PostgreSQL Hyperscale
description: Sauvegarde et restauration pour les groupes de serveurs Azure Database pour PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: 4ad0fbacdfe8b6205dd32ecd75e5291b504adcac
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111407774"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Sauvegarde et restauration des groupes de serveurs PostgreSQL Hyperscale avec Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Lorsque vous sauvegardez ou restaurez votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, l’ensemble complet des bases de données sur tous les nœuds PostgreSQL de votre groupe de serveurs sont sauvegardées et/ou restaurées.

## <a name="take-a-manual-full-backup"></a>Effectuer une sauvegarde complète manuelle

Pour effectuer une sauvegarde complète de l’ensemble des dossiers de données et de journaux de votre groupe de serveurs, exécutez la commande suivante :
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Où :
- __name__ indique le nom d’une sauvegarde.
- __server-name__ indique un groupe de serveurs
- __no-wait__ indique que la ligne de commande n’attendra pas que la sauvegarde se termine pour vous permettre de continuer à utiliser cette fenêtre de ligne de commande

Cette commande coordonne une sauvegarde complète distribuée sur tous les nœuds qui constituent votre groupe de serveurs Azure Arc enabled PostgreSQL Hyperscale. En d’autres termes, elle va sauvegarder toutes les données de vos nœuds Coordinateur et Worker.

Exemple :

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Une fois la sauvegarde terminée, l’ID, le nom, la taille, l’état et le timestamp de la sauvegarde sont retournés. Par exemple :
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` indique le fuseau horaire de l’heure à laquelle la sauvegarde a été effectuée. Dans cet exemple, « + 00:00 » correspond à l’heure UTC (UTC + 00 heure 00 minutes).

> [!NOTE]
> Les actions suivantes ne sont pas encore possibles :
> - Planification de sauvegardes automatiques
> - Montrer la progression d’une sauvegarde pendant sa réalisation

## <a name="list-backups"></a>Lister les sauvegardes

Pour lister les sauvegardes disponibles pour la restauration, exécutez la commande suivante :

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Exemple :

```console
azdata arc postgres backup list --server-name postgres01
```

Retourne une sortie comme celle-ci :

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

La colonne Timestamp indique l’heure UTC dans le temps à laquelle la sauvegarde a été effectuée.

## <a name="restore-a-backup"></a>Restaurer une sauvegarde
Dans cette section, découvrez comment effectuer une restauration complète ou une restauration à un point dans le temps. Lorsque vous restaurez une sauvegarde complète, vous restaurez la totalité du contenu de la sauvegarde. Lorsque vous effectuez une restauration à un point dans le temps, vous restaurez jusqu’à l’heure que vous indiquez. Les transactions effectuées après ce point dans le temps ne sont pas restaurées.

> [!CAUTION]
> Vous pouvez uniquement effectuer une restauration vers un groupe de serveurs qui a le même nombre de nœuds Worker qu’au moment où la sauvegarde a été effectuée. Si vous augmentez ou réduisez le nombre de nœuds Worker depuis la sauvegarde, avant de procéder à la restauration, vous devez augmenter/réduire le nombre de nœuds Worker, ou créer un nouveau groupe de serveurs, pour qu’il corresponde au contenu de la sauvegarde. La restauration échoue lorsque le nombre de nœuds Worker ne correspond pas.

### <a name="restore-a-full-backup"></a>Restaurer une sauvegarde complète
Pour restaurer l’intégralité du contenu d’une sauvegarde, exécutez la commande :
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Où :
- __backup-id__ est l’ID de la sauvegarde indiqué dans la commande list backup indiquée ci-dessous.
Celle-ci va coordonner une restauration complète distribuée sur tous les nœuds qui constituent votre groupe de serveurs Azure Arc enabled PostgreSQL Hyperscale. En d’autres termes, elle va restaurer toutes les données de vos nœuds Coordinateur et Worker.

#### <a name="examples"></a>Exemples :

__Restaurer le groupe de serveurs postgres01 sur lui-même :__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Cette opération est uniquement prise en charge pour PostgreSQL version 12 et ultérieure.

__Restaurer le groupe de serveurs postgres01 dans un autre groupe de serveurs postgres02 :__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Cette opération est prise en charge sur toutes les versions de PostgreSQL à partir de la version 11. Le groupe de serveurs cible doit être créé avant l’opération de restauration, il doit avoir la même configuration et doit utiliser le même PVC de sauvegarde que le groupe de serveurs source.

Une fois l’opération de restauration terminée, une sortie semblable à celle-ci est retournée à la ligne de commande :

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Les actions suivantes ne sont pas encore possibles :
> - Restaurer une sauvegarde en indiquant son nom
> - Montrer la progression d’une opération de restauration


### <a name="do-a-point-in-time-restore"></a>Effectuer une restauration à un point dans le temps

Pour restaurer un groupe de serveurs jusqu’à un point spécifique dans le temps, exécutez la commande suivante :
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Pour lire le format général de la commande restore, exécutez : `azdata arc postgres backup restore --help`.

Où `time` est le point dans le temps auquel effectuer la restauration. Fournissez un timestamp ou un nombre et un suffixe (`m` pour les minutes, `h` pour les heures, `d` pour les jours ou `w` pour les semaines). Par exemple, `1.5h` effectue une restauration à un point situé 90 minutes plus tôt.

#### <a name="examples"></a>Exemples :
__Effectuer une restauration à un point dans le temps du groupe de serveurs postgres01 sur lui-même :__

Il n’est pas encore possible d’effectuer une restauration dans le temps d’un groupe de serveurs sur lui-même.

__Effectuer une restauration à un point dans le temps du groupe de serveurs postgres01 vers un autre groupe de serveurs postgres02 en définissant un timestamp spécifique :__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Cet exemple restaure le groupe de serveurs postgres02 dans l’état auquel le groupe de serveurs postgres01 était le 8 décembre 2020 à 04:23:48.75 UTC. Notez que « + 00 » indique le fuseau horaire du point dans le temps que vous indiquez. Si vous n’indiquez pas de fuseau horaire, le fuseau horaire du client à partir duquel vous exécutez l’opération de restauration est utilisé.

Par exemple :
- `2020-12-08 04:23:48.751326+00` est interprété comme `2020-12-08 04:23:48.751326` UTC
- Si vous vous trouvez dans le fuseau horaire Pacifique (PST = UTC + 08), `2020-12-08 04:23:48.751326` est interprété comme `2020-12-08 12:23:48.751326` UTC. Cette opération est prise en charge pour toutes les versions de PostgreSQL à partir de la version 11. Le groupe de serveurs cible doit être créé avant l’opération de restauration et doit utiliser le même PVC de sauvegarde que le groupe de serveurs source.


__Effectuer une restauration à un point dans le temps du groupe de serveurs postgres01 vers un autre groupe de serveurs postgres02 en définissant une durée spécifique dans le passé :__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Cet exemple restaure le groupe de serveurs postgres02 dans l’état auquel le groupe de serveurs postgres01 était 22 minutes plus tôt.
Cette opération est prise en charge sur toutes les versions de PostgreSQL à partir de la version 11. Le groupe de serveurs cible doit être créé avant l’opération de restauration et doit utiliser le même PVC de sauvegarde que le groupe de serveurs source.

> [!NOTE]
> Les actions suivantes ne sont pas encore possibles :
> - Montrer la progression d’une opération de restauration

## <a name="delete-backups"></a>Supprimer les sauvegardes

La durée de conservation des sauvegardes ne peut pas être définie dans la préversion. Vous pouvez cependant supprimer manuellement les sauvegardes dont vous n’avez pas besoin.
La commande générale pour supprimer des sauvegardes est la suivante :

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

où :
- `--server-name` correspond au nom du groupe de serveurs à partir duquel l’utilisateur souhaite supprimer une sauvegarde
- `--name` correspond au nom de la sauvegarde à supprimer
- `-id` correspond à l’ID de la sauvegarde à supprimer

> [!NOTE]
> `--name` and `-id` s'excluent mutuellement.

Par exemple :

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Vous pouvez récupérer le nom et l’ID de vos sauvegardes en exécutant la commande list backup comme expliqué dans le paragraphe précédent.

Pour plus d’informations sur la commande delete, exécutez la commande suivante :

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [effectuer un scale-out (ajout de nœuds Worker)](scale-out-in-postgresql-hyperscale-server-group.md) de votre groupe de serveurs
- Découvrez comment [effectuer un scale-up ou un scale-down (augmentation/diminution de la mémoire/des vCores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) de votre groupe de serveurs
