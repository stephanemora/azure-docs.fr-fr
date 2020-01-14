---
title: Tutoriel - Restauration de base de données SAP HANA sur Azure à l’aide de l’interface CLI
description: Dans ce tutoriel, vous allez découvrir comment restaurer des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure à partir d’un coffre Recovery Services de Sauvegarde Azure à l’aide d’Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470404"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutoriel : Restaurer des bases de données SAP HANA sur une machine virtuelle Azure à l’aide d’Azure CLI

Azure CLI permet de créer et de gérer des ressources Azure à partir de la ligne de commande ou par le biais de scripts. Ce document explique comment restaurer une base de données SAP HANA sauvegardée sur une machine virtuelle Azure à l’aide d’Azure CLI. Vous pouvez également effectuer ces étapes à l’aide du [portail Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Utilisez [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) pour exécuter les commandes CLI.

À la fin de ce tutoriel, vous serez en mesure d’effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Afficher les points de restauration d’une base de données sauvegardée
> * Restaurer une base de données

Ce tutoriel part du principe que vous disposez d’une base de données SAP HANA s’exécutant sur une machine virtuelle Azure qui est sauvegardée à l’aide de Sauvegarde Azure. Si vous avez appliqué la procédure de l’article [Sauvegarder une base de données SAP HANA dans Azure à l’aide de l’interface CLI](tutorial-sap-hana-backup-cli.md) pour sauvegarder votre base de données SAP HANA, vous utiliserez les ressources suivantes :

* Un groupe de ressources nommé *saphanaResourceGroup*
* Un coffre nommé *saphanaVault*
* Un conteneur protégé nommé *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* Un élément/base de données sauvegardé nommé *saphanadatabase;hxe;hxe*
* Des ressources dans la région *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Afficher les points de restauration d’une base de données sauvegardée

Pour afficher la liste de tous les points de récupération d’une base de données, utilisez l’applet de commande [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) :

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

La liste des points de récupération se présente comme suit :

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Comme vous pouvez le voir, la liste ci-dessus contient trois points de récupération : un pour la sauvegarde complète, un pour la sauvegarde différentielle et un pour la sauvegarde des journaux.

>[!NOTE]
>Vous pouvez également afficher les points de début et de fin de chaque chaîne de sauvegarde de journaux non interrompue à l’aide de l’applet de commande [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain).

## <a name="prerequisites-to-restore-a-database"></a>Prérequis pour restaurer une base de données

Avant de restaurer une base de données, vérifiez que les prérequis suivants sont remplis :

* Vous pouvez restaurer la base de données uniquement sur une instance de SAP HANA qui se trouve dans la même région.
* L’instance cible doit être inscrite auprès du même coffre que la source.
* Sauvegarde Azure ne peut pas identifier deux instances de SAP HANA différentes sur la même machine virtuelle. Ainsi, il est impossible de restaurer des données d’une instance vers une autre sur la même machine virtuelle.

## <a name="restore-a-database"></a>Restaurer une base de données

Sauvegarde Azure peut restaurer des bases de données SAP HANA s’exécutant sur des machines virtuelles Azure comme suit :

* Restaurer à une date ou une heure spécifique (à la seconde), en utilisant les sauvegardes de fichiers journaux. Sauvegarde Azure détermine automatiquement les sauvegardes différentielles complètes appropriées, et la chaîne des sauvegardes de fichiers journaux nécessaires pour restaurer en fonction de la date/heure sélectionnée.
* Restaurer en fonction d’une sauvegarde complète ou différentielle spécifique à un point de récupération spécifique.

Pour restaurer une base de données, utilisez l’applet de commande [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl), qui nécessite entre autres un objet de configuration de récupération comme entrée. Vous pouvez générer cet objet à l’aide de l’applet de commande [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). L’objet de configuration de récupération contient tous les détails nécessaires pour effectuer une restauration, notamment le mode de restauration : **OriginalWorkloadRestore** ou **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** : restaure les données sur la même instance de SAP HANA que la source d’origine. Cette option remplace la base de données d’origine. <br>
> **AlternateWorkloadRestore** : restaure la base de données vers un autre emplacement et conserve la base de données source d’origine.

## <a name="restore-to-alternate-location"></a>Restaurer à un autre emplacement

Pour restaurer une base de données à un autre emplacement, utilisez **AlternateWorkloadRestore** comme mode de restauration. Vous devez ensuite choisir le point de restauration, qui peut être un point antérieur dans le temps ou l’un des points de restauration précédents.

Dans ce tutoriel, vous allez restaurer à un point de restauration précédent. [Affichez la liste des points de restauration](#view-restore-points-for-a-backed-up-database) pour la base de données et choisissez celui vers lequel vous souhaitez effectuer la restauration. Ce tutoriel utilise le point de restauration nommé *7660777527047692711*.

À l’aide du nom du point de restauration et du mode de restauration ci-dessus, nous allons créer l’objet de configuration de récupération avec l’applet de commande [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). Examinons ce que signifie chacun des autres paramètres de cette applet de commande :

* **--target-item-name** : il s’agit du nom qu’aura la base de données restaurée. Ici, nous avons utilisé le nom *restored_database*.
* **--target-server-name** : il s’agit du nom d’un serveur SAP HANA qui est correctement inscrit auprès d’un coffre Recovery Services et se trouve dans la même région que la base de données à restaurer. Dans ce tutoriel, nous allons restaurer la base de données sur le serveur SAP HANA que nous avons protégé, nommé *hxehost*.
* **--target-server-type** : pour la restauration des bases de données SAP HANA, vous devez utiliser **SapHanaDatabase**.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

La réponse à la requête ci-dessus est un objet de configuration de récupération qui ressemble à ceci :

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

À présent, pour restaurer la base de données, exécutez l’applet de commande [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Pour utiliser cette commande, nous allons entrer la sortie JSON ci-dessus enregistrée dans un fichier nommé *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

La sortie se présente ainsi :

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

La réponse vous donnera le nom du travail. Vous pouvez utiliser ce nom pour effectuer le suivi de l’état du travail à l’aide de l’applet de commande [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="restore-and-overwrite"></a>Restaurer et remplacer

Pour restaurer à l’emplacement d’origine, nous allons utiliser **OrignialWorkloadRestore** comme mode de restauration. Vous devez ensuite choisir le point de restauration, qui peut être un point antérieur dans le temps ou l’un des points de restauration précédents.

Pour ce tutoriel, nous allons choisir le point dans le temps précédent « 28-11-2019-09:53:00 » pour la restauration. Vous pouvez spécifier ce point de restauration aux formats suivants : jj-mm-aaaa, jj-mm-aaaa-hh:mm:ss. Pour choisir un point dans le temps valide pour la restauration, utilisez l’applet de commande [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain), qui liste les intervalles de sauvegardes de chaîne de journaux non interrompues.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

La réponse à la requête ci-dessus est un objet de configuration de récupération qui ressemble à ceci :

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

À présent, pour restaurer la base de données, exécutez l’applet de commande [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Pour utiliser cette commande, nous allons entrer la sortie JSON ci-dessus enregistrée dans un fichier nommé *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

La sortie se présente ainsi :

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

La réponse vous donnera le nom du travail. Vous pouvez utiliser ce nom pour effectuer le suivi de l’état du travail à l’aide de l’applet de commande [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment gérer des bases de données SAP HANA qui sont sauvegardées à l’aide d’Azure CLI, consultez le tutoriel [Gérer une base de données SAP HANA sur une machine virtuelle Azure à l’aide de l’interface CLI](tutorial-sap-hana-backup-cli.md).

* Pour découvrir comment restaurer une base de données SAP HANA exécutée sur une machine virtuelle Azure à l’aide du portail Azure, consultez [Restaurer une base de données SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).
