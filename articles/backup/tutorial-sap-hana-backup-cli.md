---
title: Tutoriel - Sauvegarde de base de données SAP HANA sur Azure à l’aide de l’interface CLI
description: Dans ce tutoriel, vous allez découvrir comment sauvegarder des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure dans un coffre Recovery Services de Sauvegarde Azure à l’aide de l’interface CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 7d1c52a846b837d47aa40c8f6a68010a8e7f1137
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747292"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutoriel : Sauvegarder des bases de données SAP HANA sur une machine virtuelle Azure à l’aide de l’interface CLI

Azure CLI permet de créer et de gérer des ressources Azure à partir de la ligne de commande ou par le biais de scripts. Cette documentation explique comment sauvegarder une base de données SAP HANA et déclencher des sauvegardes à la demande à l’aide d’Azure CLI. Vous pouvez également effectuer ces étapes à l’aide du [portail Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

Ce document part du principe que vous disposez déjà d’une base de données SAP HANA installée sur une machine virtuelle Azure. (Vous pouvez également [créer une machine virtuelle à l’aide d’Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).) À la fin de ce tutoriel, vous serez en mesure d’effectuer les opérations suivantes :

> [!div class="checklist"]
>
> * Créer un coffre Recovery Services
> * Inscrire une instance de SAP HANA et découvrir la ou les bases de données qu’elle contient
> * Activer la sauvegarde sur une base de données SAP HANA
> * Déclencher une sauvegarde à la demande

Consultez les [scénarios actuellement pris en charge](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) pour SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pour installer et utiliser l’interface CLI localement, vous devez exécuter Azure CLI version xx.xxx.x ou ultérieure. Pour trouver la version de CLI, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services est un conteneur logique qui stocke les données de sauvegarde de chaque ressource protégée, telles que des machines virtuelles Azure ou des charges de travail exécutées sur des machines virtuelles Azure, comme les bases de données SQL ou HANA. Lorsque le travail de sauvegarde d’une ressource protégée s’exécute, il crée un point de récupération à l’intérieur du coffre Recovery Services. Vous pouvez ensuite utiliser un de ces points de récupération pour restaurer des données à un moment donné dans le temps.

Créez un coffre Recovery Services avec [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Spécifiez le même groupe de ressources et le même emplacement que ceux de la machine virtuelle que vous souhaitez protéger. Découvrez comment créer une machine virtuelle à l’aide d’Azure CLI avec ce [guide de démarrage rapide](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Pour ce tutoriel, nous utiliserons :

* Un groupe de ressources nommé *saphanaResourceGroup*.
* Une machine virtuelle nommée *saphanaVM*.
* Des ressources situées à l’emplacement *westus2*.

Nous allons créer un coffre nommé *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Par défaut, le coffre Recovery Services est défini pour le stockage géoredondant. Le stockage géoredondant (GRS) s’assure que les données de sauvegarde sont répliquées dans une région Azure secondaire située à des centaines de kilomètres de la région primaire. Si vous devez modifier le paramètre de redondance de stockage, utilisez l’applet de commande [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set).

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Pour voir si votre coffre a bien été créé, utilisez l’applet de commande [az backup vault list](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list). Vous obtiendrez la réponse suivante :

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Inscrire et protéger l’instance de SAP HANA

Pour que l’instance de SAP HANA (la machine virtuelle avec SAP HANA installée) soit découverte par les services Azure, un [script de pré-inscription](https://aka.ms/scriptforpermsonhana) doit être exécuté sur l’ordinateur SAP HANA. Vérifiez que tous les [prérequis](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) sont respectés avant d’exécuter le script. Pour en savoir plus sur ce que fait le script, reportez-vous à la section [Ce que fait le script de préinscription](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

Une fois le script exécuté, l’instance de SAP HANA peut être inscrite auprès du coffre Recovery Services créé précédemment. Pour inscrire l’instance, utilisez l’applet de commande [az backup container register](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register). *VMResourceId* est l’ID de ressource de la machine virtuelle que vous avez créée pour installer SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Si la machine virtuelle n’est pas dans le même groupe de ressources que le coffre, *saphanaResourceGroup* fait référence au groupe de ressources dans lequel le coffre a été créé.

L’inscription de l’instance de SAP HANA entraîne la découverte automatique de toutes ses bases de données actuelles. Toutefois, pour découvrir les nouvelles bases de données qui peuvent être ajoutées ultérieurement, reportez-vous à la section [Découverte des nouvelles bases de données ajoutées à l’instance de SAP HANA inscrite](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance).

Pour vérifier si l’instance de SAP HANA est correctement inscrite auprès de votre coffre, utilisez l’applet de commande [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list). Vous obtiendrez la réponse suivante :

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> La colonne « nom » dans la sortie ci-dessus fait référence au nom du conteneur. Ce nom de conteneur sera utilisé dans les sections suivantes pour activer les sauvegardes et les déclencher. Ici, il s’agit de *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Activer la sauvegarde sur une base de données SAP HANA

L’applet de commande [az backup protectable-item list](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) liste toutes les bases de données découvertes sur l’instance de SAP HANA que vous avez inscrite à l’étape précédente.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Vous devez rechercher la base de données que vous souhaitez sauvegarder dans cette liste, qui ressemblera à ceci :

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Comme vous pouvez le voir dans la sortie ci-dessus, le SID du système SAP HANA est HXE. Dans ce tutoriel, nous allons configurer la sauvegarde pour la base de données *saphanadatabase;hxe;hxe* qui réside sur le serveur *hxehost*.

Pour protéger et configurer la sauvegarde sur une base de données, nous utilisons l’applet de commande [az backup protection enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl). Spécifiez le nom de la stratégie que vous voulez utiliser. Pour créer une stratégie à l’aide de l’interface CLI, utilisez l’applet de commande [az backup policy create](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create). Pour ce tutoriel, nous allons utiliser la stratégie *sapahanaPolicy*.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Vous pouvez vérifier si la configuration de sauvegarde ci-dessus est terminée à l’aide de l’applet de commande [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list). La sortie se présente comme suit :

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

L’applet de commande [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) liste tous les travaux de sauvegarde (planifiés ou à la demande) qui ont été exécutés ou qui sont en cours d’exécution sur la base de données protégée, en plus d’autres opérations telles que l’inscription, la configuration de sauvegarde, la suppression des données de sauvegarde, et ainsi de suite.

>[!NOTE]
>La sauvegarde Azure ne s’ajuste pas automatiquement au changement d’heure lorsque vous sauvegardez une base de données SAP HANA qui s’exécute dans une machine virtuelle Azure.
>
>Modifiez la stratégie manuellement en fonction des besoins.

## <a name="trigger-an-on-demand-backup"></a>Déclencher une sauvegarde à la demande

La section ci-dessus explique comment configurer une sauvegarde planifiée. Dans celle qui suit, nous allons voir comment déclencher une sauvegarde à la demande. Nous allons utiliser l’applet de commande [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now).

>[!NOTE]
> La stratégie de conservation d’une sauvegarde à la demande est déterminée par la stratégie de conservation sous-jacente de la base de données.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

La sortie se présente comme suit :

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

La réponse vous donnera le nom du travail. Vous pouvez utiliser ce nom pour effectuer le suivi de l’état du travail à l’aide de l’applet de commande [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

>[!NOTE]
>En plus de planifier une sauvegarde complète ou différentielle, vous pouvez également en déclencher une manuellement. Les sauvegardes de journaux sont déclenchées et gérées automatiquement par SAP HANA en interne.
>
> Les sauvegardes incrémentielles ne sont actuellement pas prises en charge par Sauvegarde Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment restaurer une base de données SAP HANA sur une machine virtuelle Azure à l’aide de l’interface CLI, continuez avec le tutoriel intitulé [Restaurer une base de données SAP HANA sur une machine virtuelle Azure à l’aide de l’interface CLI](tutorial-sap-hana-restore-cli.md).

* Pour découvrir comment sauvegarder une base de données SAP HANA exécutée sur une machine virtuelle Azure à l’aide du portail Azure, consultez [Sauvegarder une base de données SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).
