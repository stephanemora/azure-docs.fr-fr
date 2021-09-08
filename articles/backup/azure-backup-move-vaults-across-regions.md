---
title: Déplacer un coffre Azure Recovery Services Azure vers une autre région
description: Cet article explique comment veiller au maintien des sauvegardes continues après avoir déplacé des ressources d’une région à une autre.
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: faf6ad49234d9753a0479151bf931714a1eba226
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116321"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Sauvegarder des ressources dans un coffre Recovery Services après déplacement de celui-ci vers une autre région

Azure Resource Mover prend en charge le déplacement de plusieurs ressources entre régions. Lorsque vous déplacez vos ressources d’une région vers une autre, vous pouvez veiller à ce que vos ressources restent protégées. Le service Sauvegarde Azure prenant en charge la protection de plusieurs charges de travail, il se peut que vous deviez prendre des mesures pour continuer à bénéficier du même niveau de protection dans la nouvelle région.

Pour comprendre les étapes détaillées à suivre à cette fin, reportez-vous aux sections ci-dessous.

>[!Note]
>Actuellement, le service Sauvegarde Azure ne prend pas en charge le déplacement de données de sauvegarde d’un coffre Recovery Services vers un autre. Pour pouvoir protéger votre ressource dans la nouvelle région, la ressource doit être inscrite et sauvegardée dans un coffre nouveau ou existant dans la nouvelle région. Lorsque vous déplacez vos ressources d’une région vers une autre, vous pouvez conserver ou supprimer les données de sauvegarde de vos coffres Recovery services existants dans l’ancienne région en fonction de vos besoins. Si vous choisissez de conserver les données dans les anciens coffres, des frais de sauvegarde vous seront facturés en conséquence.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Sauvegarder une machine virtuelle Azure après déplacement entre les régions

Quand une machine virtuelle Azure protégée par un coffre Recovery Services est déplacée d’une région vers une autre, elle ne peut plus être sauvegardée dans l’ancien coffre. Les sauvegardes dans l’ancien coffre échouent avec les erreurs **BCMV2VMNotFound** ou [**ResourceNotFound**](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found).

Pour protéger votre machine virtuelle dans la nouvelle région, procédez comme suit :

1. Avant de déplacer la machine virtuelle, [sélectionnez la machine virtuelle sous l’onglet **Éléments de sauvegarde**](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) du tableau de bord du coffre existant, sélectionnez **Arrêter la protection**, puis choisissez de conserver ou supprimer les données en fonction de vos besoins. Lorsque la sauvegarde des données d’une machine virtuelle est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie. Cela garantit que vos données de sauvegarde sont toujours prêtes pour restauration.

   >[!Note]
   >La conservation des données dans l’ancien coffre entraîne des frais de sauvegarde. Si vous ne souhaitez plus conserver les données pour éviter la facturation, vous devez supprimer les données de sauvegarde conservées à l’aide de l’[option Supprimer les données](/azure/backup/backup-azure-manage-vms#delete-backup-data).

1. Déplacez votre machine virtuelle vers la nouvelle région à l’aide d’[Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines).

1. Commencez à protéger votre machine virtuelle dans un coffre Recovery Services nouveau ou existant dans la nouvelle région.
   Lorsque vous devez effectuer une restauration de vos anciennes sauvegardes, vous pouvez toujours le faire à partir de votre ancien coffre Recovery Services si vous aviez choisi de conserver les données de sauvegarde. 

Les étapes ci-dessus vous aideront à veiller à ce que vos ressources soient également sauvegardées dans la nouvelle région.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Sauvegarder un partage de fichiers Azure après déplacement entre régions

Pour déplacer vos comptes de stockage avec les partages de fichiers qu’ils contiennent d’une région à une autre, consultez [Déplacer un compte Stockage Azure vers une autre région](/azure/storage/common/storage-account-move).

>[!Note]
>Quand un partage de fichiers Azure est copié entre régions, les captures instantanées qui lui sont associés ne sont pas déplacées en même temps. Pour déplacer les données des captures instantanées vers la nouvelle région, vous devez déplacer les fichiers et répertoires individuels de celles-ci vers le compte de stockage dans la nouvelle région à l’aide de la commande [AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account).

Le service Sauvegarde Azure offre à présent une [solution de gestion des captures instantanées](/azure/backup/backup-afs#discover-file-shares-and-configure-backup) pour Azure Files. Cela signifie que vous ne déplacez pas les données de partage de fichiers dans les coffres Recovery Services. Par ailleurs, étant donné que les captures instantanées ne sont pas déplacées avec votre compte de stockage, toutes vos sauvegardes (captures instantanées) sont conservées uniquement dans la région existante, et protégées par le coffre existant. Toutefois, vous pouvez veiller à ce que les nouveaux partages de fichiers que vous créez dans la nouvelle région soient protégés par le service Sauvegarde Azure en procédant comme suit :

1. Commencez à protéger le partage de fichiers Azure copié dans le nouveau compte de stockage dans un coffre Recovery Services nouveau ou existant dans la nouvelle région.  

1. Une fois le partage de fichiers Azure copié dans la nouvelle région, vous pouvez choisir d’arrêter la protection et de conserver ou supprimer les captures instantanées (et les points de récupération correspondants) du partage de fichiers Azure d’origine en fonction de vos besoins. Pour ce faire, vous pouvez sélectionner votre partage de fichiers sous l’[onglet Éléments de sauvegarde](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) du tableau de bord du coffre d’origine. Lorsque la sauvegarde des données du partage de fichiers Azure est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie.
   
   Cela garantit que vos instantanés seront toujours prêts pour restauration à partir de l’ancien coffre. 
 
## <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>Sauvegarder SQL Server ou SAP HANA dans une machine virtuelle Azure

Lorsque vous déplacez une machine virtuelle exécutant des serveurs SQL ou SAP HANA vers une autre région, les bases de données SQL et SAP HANA dans ces machines virtuelles ne peuvent plus être sauvegardées dans le coffre de la région précédente. Pour protéger les serveurs SQL et SAP HANA qui s’exécutent dans la machine virtuelle Azure dans la nouvelle région, procédez comme suit :
 
1. Avant de déplacer une machine virtuelle exécutant SQL Server ou SAP HANA vers une nouvelle région, sélectionnez-la sous l’[onglet Éléments de sauvegarde](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) du tableau de bord du coffre existant, puis sélectionnez les _bases de données_ dont la sauvegarde doit être arrêtée. Sélectionnez **Arrêter la protection**, puis choisissez de conserver ou supprimer les données selon vos besoins. Lorsque la sauvegarde des données est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie. Cela garantit que vos données de sauvegarde sont toujours prêtes pour restauration.

   >[!Note]
   >La conservation des données dans l’ancien coffre entraîne des frais de sauvegarde. Si vous ne souhaitez plus conserver les données pour éviter la facturation, vous devez supprimer les données de sauvegarde conservées à l’aide de l’[option Supprimer les données](/azure/backup/backup-azure-manage-vms#delete-backup-data).

1. Déplacez la machine virtuelle exécutant SQL Server/SAP HANA vers la nouvelle région à l’aide d’[Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines).

1. Commencez à protéger la machine virtuelle dans un coffre Recovery Services nouveau ou existant dans la nouvelle région. Lorsque vous devez restaurer d’anciennes sauvegardes, vous pouvez toujours le faire à partir de votre ancien coffre Recovery Services.
 
Les étapes ci-dessus vous aideront à veiller à ce que vos ressources soient également sauvegardées dans la nouvelle région.