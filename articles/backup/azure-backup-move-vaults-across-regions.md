---
title: Déplacer un coffre Azure Recovery Services Azure vers une autre région
description: Cet article explique comment veiller au maintien des sauvegardes continues après avoir déplacé des ressources d’une région à une autre.
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: how-to
ms.openlocfilehash: 2b3804a0a73329d7b2e4e2449fe773918791dcea
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264938"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Sauvegarder des ressources dans un coffre Recovery Services après déplacement de celui-ci vers une autre région

Azure Resource Mover prend en charge le déplacement de plusieurs ressources entre régions. Lorsque vous déplacez vos ressources d’une région vers une autre, vous pouvez veiller à ce que vos ressources restent protégées. Le service Sauvegarde Azure prenant en charge la protection de plusieurs charges de travail, il se peut que vous deviez prendre des mesures pour continuer à bénéficier du même niveau de protection dans la nouvelle région.

Pour comprendre les étapes détaillées à suivre à cette fin, reportez-vous aux sections ci-dessous.

>[!Note]
>Actuellement, le service Sauvegarde Azure ne prend pas en charge le déplacement de données de sauvegarde d’un coffre Recovery Services vers un autre. Pour pouvoir protéger votre ressource dans la nouvelle région, la ressource doit être inscrite et sauvegardée dans un coffre nouveau ou existant dans la nouvelle région. Quand vous déplacez vos ressources d’une région à une autre, vous pouvez conserver ou supprimer les données de sauvegarde de vos coffres Recovery Services existants dans l’ancienne région en fonction de vos besoins. Si vous choisissez de conserver les données dans les anciens coffres, des frais de sauvegarde vous seront facturés en conséquence.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Sauvegarder une machine virtuelle Azure après déplacement entre les régions

Quand une machine virtuelle Azure protégée par un coffre Recovery Services est déplacée d’une région vers une autre, elle ne peut plus être sauvegardée dans l’ancien coffre. Les sauvegardes dans l’ancien coffre échouent avec les erreurs **BCMV2VMNotFound** ou [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Pour plus d’informations sur la protection de vos machines virtuelles dans la nouvelle région, consultez les sections suivantes.

### <a name="prepare-to-move-azure-vms"></a>Préparer le déplacement des machines virtuelles Azure

Avant de déplacer une machine virtuelle, veillez à remplir les prérequis suivants :

1. Consultez les [prérequis associés au déplacement d’une machine virtuelle](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites) et vérifiez que la machine virtuelle est éligible au déplacement.
1. [Sélectionnez la machine virtuelle sous l’onglet **Éléments de sauvegarde**](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) du tableau de bord du coffre existant, sélectionnez **Arrêter la protection**, puis conservez ou supprimez les données selon vos besoins. Lorsque la sauvegarde des données d’une machine virtuelle est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie. Cela garantit que vos données de sauvegarde sont toujours prêtes pour restauration.
   >[!Note]
   >La conservation des données dans l’ancien coffre entraîne des frais de sauvegarde. Si vous ne souhaitez plus conserver les données pour éviter la facturation, vous devez supprimer les données de sauvegarde conservées à l’aide de l’[option Supprimer les données](./backup-azure-manage-vms.md#delete-backup-data).
1. Vérifiez que les machines virtuelles sont sous tension. Tous les disques de machines virtuelles qui doivent être disponibles dans la région de destination sont attachés et initialisés dans les machines virtuelles.
1. Vérifiez que les machines virtuelles disposent des certificats racines approuvés les plus récents et d’une liste de révocation de certificats mise à jour. Pour cela, procédez de la façon suivante :
   - Sur les machines virtuelles Windows, installez les dernières mises à jour Windows.
   - Sur les machines virtuelles Linux, suivez les conseils d’aide du distributeur pour vérifier qu’elles disposent des derniers certificats et des dernières listes de révocation de certificats.
1. Autorisez une connexion sortante à partir des machines virtuelles :
   - Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès à ces [URL](../resource-mover/support-matrix-move-region-azure-vm.md#url-access).
   - Si vous utilisez des règles NSG (groupe de sécurité réseau) pour contrôler la connectivité sortante, créez ces [règles d’étiquette de service](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules).

### <a name="move-azure-vms"></a>Déplacer des machines virtuelles Azure

Déplacez votre machine virtuelle vers la nouvelle région à l’aide d’[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md).

### <a name="protect-azure-vms-using-azure-backup"></a>Protéger les machines virtuelles Azure à l’aide de Sauvegarde Azure

Commencez à protéger votre machine virtuelle dans un coffre Recovery Services nouveau ou existant dans la nouvelle région. Lorsque vous devez effectuer une restauration de vos anciennes sauvegardes, vous pouvez toujours le faire à partir de votre ancien coffre Recovery Services si vous aviez choisi de conserver les données de sauvegarde. 

Les étapes ci-dessus vous aideront à veiller à ce que vos ressources soient également sauvegardées dans la nouvelle région.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Sauvegarder un partage de fichiers Azure après déplacement entre régions

Le service Sauvegarde Azure offre à présent une [solution de gestion des captures instantanées](./backup-afs.md) pour Azure Files. Cela signifie que vous ne déplacez pas les données de partage de fichiers dans les coffres Recovery Services. Par ailleurs, étant donné que les captures instantanées ne sont pas déplacées avec votre compte de stockage, toutes vos sauvegardes (captures instantanées) sont conservées uniquement dans la région existante, et protégées par le coffre existant. Toutefois, si vous déplacez vos comptes Stockage et les partages de fichiers entre différentes régions, ou si vous créez des partages de fichiers dans la nouvelle région, consultez les sections suivantes pour vérifier qu’ils sont protégés par le service Sauvegarde Azure.

### <a name="prepare-to-move-azure-file-share"></a>Préparer le déplacement du partage de fichiers Azure

Avant de déplacer le compte Stockage, vérifiez que les prérequis suivants sont remplis :

1.  Consultez les [prérequis liés au déplacement du compte Stockage](../storage/common/storage-account-move.md?tabs=azure-portal#prerequisites). 
1. Exporter et modifier un modèle de déplacement de ressource. Pour plus d’informations, consultez [Préparer le compte Stockage à un changement de région](../storage/common/storage-account-move.md?tabs=azure-portal#prepare).

### <a name="move-azure-file-share"></a>Déplacer le partage de fichiers Azure

Pour déplacer vos comptes Stockage et les partages de fichiers qu’ils contiennent d’une région à une autre, consultez [Déplacer un compte Stockage Azure vers une autre région](../storage/common/storage-account-move.md).

>[!Note]
>Quand un partage de fichiers Azure est copié entre régions, les captures instantanées qui lui sont associés ne sont pas déplacées en même temps. Pour déplacer les données des captures instantanées vers la nouvelle région, vous devez déplacer les fichiers et répertoires individuels de celles-ci vers le compte de stockage dans la nouvelle région à l’aide de la commande [AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account).

### <a name="protect-azure-file-share-using-azure-backup"></a>Protéger le partage de fichiers Azure à l’aide de Sauvegarde Azure

Commencez à protéger le partage de fichiers Azure copié dans le nouveau compte de stockage dans un coffre Recovery Services nouveau ou existant dans la nouvelle région.  

Une fois le partage de fichiers Azure copié dans la nouvelle région, vous pouvez choisir d’arrêter la protection et de conserver ou supprimer les captures instantanées (et les points de récupération correspondants) du partage de fichiers Azure d’origine en fonction de vos besoins. Pour ce faire, vous pouvez sélectionner votre partage de fichiers sous l’[onglet Éléments de sauvegarde](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) du tableau de bord du coffre d’origine. Lorsque la sauvegarde des données du partage de fichiers Azure est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie.
   
Cela garantit que vos instantanés seront toujours prêts pour restauration à partir de l’ancien coffre. 
 
## <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>Sauvegarder SQL Server/SAP HANA sur une machine virtuelle Azure après un changement de région

Lorsque vous déplacez une machine virtuelle exécutant des serveurs SQL ou SAP HANA vers une autre région, les bases de données SQL et SAP HANA dans ces machines virtuelles ne peuvent plus être sauvegardées dans le coffre de la région précédente. Pour protéger les serveurs SQL et SAP HANA s’exécutant sur une machine virtuelle Azure dans la nouvelle région, consultez les sections suivantes.

### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Préparer le déplacement de SQL Server/SAP HANA sur une machine virtuelle Azure

Avant de déplacer SQL Server/SAP HANA s’exécutant sur une machine virtuelle vers une nouvelle région, vérifiez que les prérequis suivants sont remplis :

1. Consultez les [prérequis associés au déplacement d’une machine virtuelle](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites) et vérifiez que la machine virtuelle est éligible au déplacement. 
1. Sélectionnez la machine virtuelle sous l’onglet [Éléments de sauvegarde](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) du tableau de bord du coffre existant, puis sélectionnez les _bases de données_ dont la sauvegarde doit être arrêtée. Sélectionnez **Arrêter la protection**, puis choisissez de conserver ou supprimer les données selon vos besoins. Lorsque la sauvegarde des données est arrêtée avec conservation des données, les points de récupération restent indéfiniment et n’adhèrent à aucune stratégie. Cela garantit que vos données de sauvegarde sont toujours prêtes pour restauration.
   >[!Note]
   >La conservation des données dans l’ancien coffre entraîne des frais de sauvegarde. Si vous ne souhaitez plus conserver les données pour éviter la facturation, vous devez supprimer les données de sauvegarde conservées à l’aide de l’[option Supprimer les données](./backup-azure-manage-vms.md#delete-backup-data).
1. Vérifiez que les machines virtuelles à déplacer sont sous tension. Tous les disques de machines virtuelles qui doivent être disponibles dans la région de destination sont attachés et initialisés dans les machines virtuelles.
1. Vérifiez que les machines virtuelles disposent des certificats racines approuvés les plus récents et d’une liste de révocation de certificats mise à jour. Pour cela, procédez de la façon suivante :
   - Sur les machines virtuelles Windows, installez les dernières mises à jour Windows.
   - Sur les machines virtuelles Linux, suivez les conseils d’aide du distributeur pour vérifier qu’elles disposent des derniers certificats et des dernières listes de révocation de certificats.
1. Autorisez une connexion sortante à partir des machines virtuelles :
   - Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès à ces [URL](../resource-mover/support-matrix-move-region-azure-vm.md#url-access).
   - Si vous utilisez des règles NSG (groupe de sécurité réseau) pour contrôler la connectivité sortante, créez ces [règles d’étiquette de service](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules).

### <a name="move-sql-serversap-hana-in-azure-vm"></a>Déplacer SQL Server/SAP HANA sur une machine virtuelle Azure

Déplacez votre machine virtuelle vers la nouvelle région à l’aide d’[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md).

### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Protéger SQL Server/SAP HANA sur une machine virtuelle Azure à l’aide de Sauvegarde Azure

Commencez à protéger la machine virtuelle dans un coffre Recovery Services nouveau ou existant dans la nouvelle région. Lorsque vous devez restaurer d’anciennes sauvegardes, vous pouvez toujours le faire à partir de votre ancien coffre Recovery Services.
 
Les étapes ci-dessus vous aideront à veiller à ce que vos ressources soient également sauvegardées dans la nouvelle région.