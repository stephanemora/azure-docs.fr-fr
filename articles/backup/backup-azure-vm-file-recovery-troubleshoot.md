---
title: Résoudre les problèmes de récupération de fichiers d’une machine virtuelle Azure
description: Résolvez les problèmes liés à la récupération de fichiers et de dossiers à partir d’une sauvegarde de machine virtuelle Azure.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700301"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Résolution des problèmes de récupération de fichiers d’une sauvegarde de machine virtuelle Azure

Cet article présente les étapes de dépannage qui peuvent vous aider à résoudre les erreurs de récupération de fichiers et de dossiers à partir d’une sauvegarde de machine virtuelle Azure.

## <a name="common-error-messages"></a>Messages d’erreur courants

Cette section décrit les étapes à suivre pour résoudre les messages d’erreur pouvant s’afficher.

### <a name="exception-caught-while-connecting-to-target"></a>« Exception interceptée pendant la connexion à la cible »

**Cause possible** : Le script n’est pas en mesure d’accéder au point de récupération.

**Action recommandée** : Pour résoudre ce problème, suivez les étapes indiquées dans [Le script s’exécute, mais la connexion a échoué](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>« La cible a déjà été connectée via une session iSCSI »

**Cause possible** : Le script a déjà été exécuté sur la même machine et les lecteurs ont été joints.

**Action recommandée** : Les volumes du point de récupération ont déjà été connectés. Ils ne peuvent pas être montés avec les mêmes lettres de lecteur que celles de la machine virtuelle d’origine. Parcourez les volumes disponibles dans l’Explorateur de fichiers.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>« Ce script n’est pas valide, car les disques ont été démontés via le portail/ont dépassé la limite de 12 h. Télécharger un nouveau script à partir du portail »

**Cause possible** : Les disques ont été démontés à partir du portail ou la limite de temps de 12 heures a été dépassée.

**Action recommandée** : 12 heures après le téléchargement du script, celui-ci devient non valide et ne peut plus être exécuté. Accédez au portail et téléchargez un nouveau script pour poursuivre la récupération des fichiers.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>Impossible de charger le module iscsi_tcp (ou) de trouver iscsi_tcp_module

**Action recommandée** : Pour résoudre ce problème, suivez les étapes décrites dans [Le script se télécharge correctement, mais il ne s’exécute pas](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Problèmes courants

Cette section décrit les étapes à suivre pour résoudre les problèmes courants que vous pouvez rencontrer pendant le téléchargement et l’exécution du script pour la récupération de fichiers.

### <a name="you-cant-download-the-script"></a>Impossible de télécharger le script

1. Vérifiez que vous disposez des [autorisations nécessaires pour télécharger le script](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Vérifiez la connexion aux adresses IP cibles Azure. Exécutez l’une des commandes suivantes à partir d’une invite de commandes avec élévation de privilèges :

   `nslookup download.microsoft.com`

    or

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Le script se télécharge correctement, mais il ne s’exécute pas

Pendant l’exécution du script Python pour la récupération au niveau de l’élément (ILR) sur le système d’exploitation SUSE Linux Enterprise Server 12 SP4, celui-ci échoue avec l’erreur « Impossible de charger le module iscsi_tcp » ou « Impossible de trouver iscsi_tcp_module ».

**Cause possible** : Le module ILR utilise **iscsi_tcp** pour établir une connexion TCP au service de sauvegarde. Dans le cadre de la version SLES 12 SP4, SUSE a supprimé **iscsi_tcp** du package open-iscsi, ce qui entraîne l’échec de l’opération ILR.

**Action recommandée** :  L’exécution de scripts de récupération de fichiers n’est pas prise en charge sur les machines virtuelles SUSE 12 SP4. Essayez l’opération de restauration sur une version antérieure de SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Le script s’exécute, mais la connexion à la cible iSCSI échoue

Le message d’erreur « Exception interceptée pendant la connexion à la cible » peut s’afficher.

1. Vérifiez que la machine sur laquelle le script est exécuté remplit les [conditions d’accès](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Vérifiez la connexion aux adresses IP cibles Azure. Exécutez l’une des commandes suivantes à partir d’une invite de commandes avec élévation de privilèges :

   `nslookup download.microsoft.com`

   or

   `ping download.microsoft.com`
1. Vérifiez qu’il y a un accès au port de sortie iSCSI 3260.
1. Vérifiez si un pare-feu ou un groupe de sécurité réseau (NSG) bloque le trafic vers les adresses IP cibles Azure ou les URL du service de récupération.
1. Assurez-vous que votre antivirus ne bloque pas l’exécution du script.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Vous êtes connecté au point de récupération, mais les disques n’ont pas été joints

Résolvez ce problème en effectuant les étapes ci-dessous pour votre système d’exploitation.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Échec de la récupération des fichiers Windows sur le serveur avec les pools de stockage

Lors de la première exécution du script sur Windows Server 2012 R2 et Windows Server 2016 (avec pools de stockage), le pool de stockage peut être joint à la machine virtuelle en lecture seule.

>[!Tip]
> Vérifiez que votre [machine permet d’exécuter le script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Pour résoudre ce problème, définissez manuellement l’accès en lecture-écriture au pool de stockage et joignez les disques virtuels :

1. Accédez à **Gestionnaire de serveur** > **Services de fichiers et de stockage** > **Volumes** > **Pools de stockage**.

   ![Capture d’écran montrant les options des pools de stockage.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Dans la fenêtre **Pool de stockage**, cliquez avec le bouton droit sur le pool de stockage disponible, puis sélectionnez **Définir l’accès en lecture-écriture**.

   ![Capture d’écran montrant les options de clic droit pour un pool de stockage.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Une fois que l’accès en lecture-écriture a été attribué au pool de stockage, cliquez avec le bouton droit dans la section **Disques virtuels** et sélectionnez **Joindre le disque virtuel**.

   ![Capture d’écran montrant les options de clic droit pour un disque virtuel.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>La récupération de fichiers Linux échoue dans l’opération de montage automatique car le disque ne contient pas de volumes

Pendant la récupération de fichiers, le service de sauvegarde détecte les volumes et procède au montage automatique. Toutefois, si les disques sauvegardés ont des partitions brutes, ces disques ne sont pas montés automatiquement et vous ne pouvez pas voir le disque de données pour la récupération.

Pour résoudre ce problème, consultez [Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Échec de la récupération de fichiers Linux car le système d’exploitation n’a pas pu identifier le système de fichiers

Lors de l’exécution du script de récupération de fichiers, le disque de données ne peut être joint. L’erreur suivante s’affiche : « Les partitions suivantes n’ont pas pu être montées, car le système d’exploitation n’a pas pu identifier le système de fichiers ».

Pour résoudre ce problème, vérifiez si le volume est chiffré avec une application tierce. Si c’est le cas, le disque ou la machine virtuelle n’apparaît pas comme étant chiffré sur le portail.

1. Connectez-vous à la machine virtuelle sauvegardée et exécutez cette commande :

   `lsblk -f`

   ![Capture d’écran montrant les résultats de la commande pour répertorier les appareils de bloc.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Vérifiez le système de fichiers et le chiffrement. Si le volume est chiffré, la récupération de fichiers n’est pas prise en charge. Pour en savoir plus, consultez le [Tableau de prise en charge pour la sauvegarde de machines virtuelles Azure](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Les disques sont joints mais les volumes ne sont pas montés

Résolvez ce problème en effectuant les étapes ci-dessous pour votre système d’exploitation.

#### <a name="windows"></a>Windows

Lorsque vous exécutez le script de récupération de fichiers pour Windows, vous voyez un message « 0 volume de récupération joint ». Toutefois, les disques sont découverts dans la console Gestion des disques.

**Cause possible** : Quand vous avez joint les volumes via iSCSI, certains volumes détectés sont passés hors connexion. Quand le canal iSCSI communique entre la machine virtuelle et le service, il détecte ces volumes et les met en ligne, mais ils ne sont pas montés.

   ![Capture d’écran montrant 0 volume de récupération joint.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Pour identifier et résoudre ce problème, effectuez les étapes suivantes :

>[!Tip]
>Vérifiez que votre [machine permet d’exécuter le script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Dans la fenêtre **cmd**, exécutez **diskmgmt** pour ouvrir **Gestion des disques**.
1. Recherchez d’autres disques. Dans l’exemple suivant, **Disque 2** est un disque supplémentaire.

   ![Capture d’écran de la fenêtre Gestion des disques avec disque supplémentaire.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Cliquez avec le bouton droit sur **Nouveau volume**, puis sélectionnez **Modifier la lettre de lecteur et les chemins d’accès**.

   ![Capture d’écran montrant les options de clic droit sur le disque supplémentaire.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Dans la fenêtre **Modifier la lettre de lecteur ou le chemin d’accès**, sélectionnez **Attribuer la lettre de lecteur suivante**, attribuez un lecteur disponible, puis sélectionnez **OK**.

   ![Capture d’écran de la fenêtre Modifier la lettre de lecteur ou le chemin d’accès.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Ouvrez l’Explorateur de fichiers pour afficher le lecteur choisi et explorer les fichiers.

#### <a name="linux"></a>Linux

>[!Tip]
>Vérifiez que votre [machine permet d’exécuter le script](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Si la machine virtuelle Linux protégée utilise des contrôleurs RAID ou LVM, suivez les étapes décrites dans [Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Impossible de copier les fichiers à partir des volumes montés

La copie peut échouer avec l’erreur « 0x80070780 : Le système ne peut pas accéder au fichier. » 

Vérifiez si la déduplication de disque est activée sur le serveur source. Si c’est le cas, assurez-vous que la déduplication est également activée sur les lecteurs du serveur de restauration. Vous pouvez laisser la déduplication non configurée pour éviter de dédupliquer les lecteurs sur le serveur de restauration.

## <a name="next-steps"></a>Étapes suivantes

- [Récupérer des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure](backup-azure-restore-files-from-vm.md)