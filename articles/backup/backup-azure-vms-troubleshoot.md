---
title: Résoudre les erreurs de sauvegarde avec les machines virtuelles Azure
description: Dépannage de la sauvegarde et de la restauration de machines virtuelles Azure
services: backup
author: srinathvasireddy
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 23137cd686bcdba59880ff705a43b16ced992b59
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303995"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Dépannage de la sauvegarde de machine virtuelle Azure
Vous pouvez résoudre les erreurs rencontrées lors de l’utilisation de la sauvegarde Azure avec les informations répertoriées ci-dessous :

## <a name="backup"></a>Sauvegarde
Cette section décrit l’échec de l’opération de sauvegarde de machine virtuelle Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - copie des données sauvegardées à partir du coffre a expiré

Code d’erreur : CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Message d’erreur : Copie des données sauvegardées à partir du coffre a expiré

Cela peut se produire en raison d’erreurs de stockage temporaire ou le compte de stockage insuffisant e/s pour le service de sauvegarde pour transférer des données vers le coffre dans le délai imparti. Configurer la sauvegarde de machine virtuelle à l’aide de ces [meilleures pratiques](backup-azure-vms-introduction.md#best-practices) et recommencez l’opération de sauvegarde.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - machine virtuelle n’est pas dans un état qui autorise les sauvegardes.

Code d’erreur : UserErrorVmNotInDesirableState <br/>
Message d’erreur : La machine virtuelle n’est pas dans un état permettant les sauvegardes.<br/>

L’opération de sauvegarde a échoué car la machine virtuelle est en état d’échec. Pour la machine virtuelle de sauvegarde réussie état doit être en cours d’exécution, arrêté ou arrêté (désalloué).

* Si la machine virtuelle se trouve dans un état temporaire entre **En cours d’exécution** et **Arrêt**, attendez que l’état change. Déclenchez ensuite le travail de sauvegarde.
*  Si la machine virtuelle est de type Linux et utilise le module de noyau Linux à sécurité avancée, excluez le chemin d’accès de l’agent Azure Linux **/var/lib/waagent** de la stratégie de sécurité, et assurez-vous que l’extension Sauvegarde Azure est installée.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Impossible de figer un ou plusieurs points de montage de la machine virtuelle pour prendre un instantané cohérent du système de fichiers

Code d’erreur : UserErrorFsFreezeFailed <br/>
Message d’erreur : Impossible de figer un ou plusieurs points de montage de la machine virtuelle pour prendre une capture instantanée cohérente au niveau du système de fichiers.

* Vérifier l’état du système de fichiers de tous les périphériques montés à l’aide de la **tune2fs** de commande, par exemple **tune2fs -l/dev/sdb1 \\** .\| grep **l’état du système de fichiers**.
* Démontez les périphériques pour lesquels l’état du système n’a pas été nettoyé, à l’aide de la **unmount** commande.
* Exécuter une vérification de cohérence de système de fichiers sur ces appareils à l’aide de la **fsck** commande.
* Remontez les périphériques et recommencez l’opération de sauvegarde.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Extension installation/opération a échoué en raison d’une erreur COM +

Code d’erreur : ExtensionSnapshotFailedCOM <br/>
Message d’erreur : Échec de l’opération de capture instantanée en raison d’une erreur COM+

Code d’erreur : ExtensionInstallationFailedCOM  <br/>
Message d’erreur : Installation / l’opération d’extension a échoué en raison d’une erreur COM +

Code d’erreur : ExtensionInstallationFailedMDTC <br/>
Message d’erreur : L’installation de l’extension a échoué en renvoyant l’erreur « COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator <br/>

L’opération de sauvegarde a échoué en raison d’un problème avec Windows service **système COM +** application.  Pour résoudre ce problème, effectuez les étapes suivantes :

* Essayez de démarrage/redémarrage du service de Windows **Application système COM +** (à partir d’une invite de commandes avec élévation de privilèges **-net start COMSysApp**).
* Vérifiez **Distributed Transaction Coordinator** services s’exécute en tant que **Service réseau** compte. Dans le cas contraire, modifiez-le pour exécuter en tant que **Service réseau** compte et redémarrez **Application système COM +** .
* S’il est impossible de redémarrer le service, puis réinstallez **Distributed Transaction Coordinator** service en suivant les étapes ci-dessous :
    * Arrêtez le service MSDTC
    * Ouvrez une invite de commandes (cmd)
    * Exécutez la commande « msdtc-désinstaller »
    * commande d’annulation « msdtc-installer »
    * Lancez le service MSDTC
* Démarrez le service Windows **Application système COM+** . Une fois que **Application système COM+** démarre, déclenchez un travail de sauvegarde à partir du Portail Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - opération de capture instantanée a échoué car les enregistreurs VSS sont dans un état incorrect

Code d’erreur : ExtensionFailedVssWriterInBadState <br/>
Message d’erreur : Opération de capture instantanée a échoué car les enregistreurs VSS sont en mauvais état.

Redémarrez les enregistreurs VSS qui se trouvent dans un état incorrect. À partir d’une invite de commandes avec élévation de privilèges, exécutez ```vssadmin list writers```. La sortie contient tous les enregistreurs VSS et leur état. Pour chaque enregistreur VSS dont l’état n’est pas **[1] Stable**, redémarrez l’enregistreur VSS en exécutant les commandes suivantes à partir d’une invite de commandes avec élévation de privilèges :

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Échec d’analyse de la configuration pour l’extension de sauvegarde

Code d’erreur : ExtensionConfigParsingFailure<br/>
Message d’erreur : Échec d’analyse de la configuration pour l’extension de sauvegarde.

Cette erreur se produit en raison de modifications des autorisations sur le répertoire **MachineKeys** : **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Exécutez la commande suivante et vérifiez que les autorisations sur le **MachineKeys** répertoire sont celles par défaut :**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Les autorisations par défaut sont comme suit :
* Tout le monde : (R,W)
* BUILTIN\Administrateurs : (F)

Si les autorisations que vous voyez dans le répertoire **MachineKeys** sont différentes de celles par défaut, suivez les étapes ci-dessous pour les corriger, supprimer le certificat et déclencher la sauvegarde :

1. Corrigez les autorisations sur le répertoire **MachineKeys**. À l’aide des propriétés de sécurité de l’explorateur et des paramètres de sécurité avancés du répertoire, réinitialisez les autorisations aux valeurs par défaut. Supprimez tous les objets utilisateur (sauf ceux par défaut) du répertoire, et assurez-vous que l’autorisation **Tout le monde** dispose d’un accès spécial comme suit :

    * Lister le dossier/lire les données
    * Lire les attributs
    * Lire les attributs étendus
    * Créer les fichiers/écrire les données
    * Créer les dossiers/ajouter les données
    * Écrire les attributs
    * Écrire les attributs étendus
    * Autorisations de lecture
2. Supprimez tous les certificats dont le modèle de déploiement classique est de type **Délivré à** ou **générateur de certificats Windows Azure CRP** :
    * [Ouvrez les certificats sur une console d’ordinateur local](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * Sous **Certificats** > **personnels**, supprimez tous les certificats dont le modèle de déploiement classique est de type **Délivré à** ou **générateur de certificats Windows Azure CRP**.
3. Lancez une opération de sauvegarde de machine virtuelle.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - état de l’Extension n’est pas d’effectuer l’opération de sauvegarde

Code d’erreur : ExtensionStuckInDeletionState <br/>
Message d’erreur : État de l’extension n’est pas d’effectuer l’opération de sauvegarde

L’opération de sauvegarde a échoué en raison d’un état incohérent de la sauvegarde. Pour résoudre ce problème, effectuez les étapes suivantes :

* Vérifiez que l’agent invité est installé et réactif
* Dans le portail Azure, accédez à **Machine virtuelle** > **Tous les paramètres** > **Extensions**
* Sélectionnez l’extension de sauvegarde VmSnapshot ou VmSnapshotLinux, puis cliquez sur **Désinstaller**
* Après avoir supprimé l’extension de sauvegarde, recommencez l’opération de sauvegarde
* L’opération de sauvegarde suivante installera la nouvelle extension à l’état souhaité

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - opération de capture instantanée a échoué, car la limite de capture instantanée est dépassée pour certaines des disques attachés

Code d’erreur : ExtensionFailedSnapshotLimitReachedError  <br/>
Message d’erreur : Opération de capture instantanée a échoué, car la limite de capture instantanée est dépassée pour certaines des disques attachés

L’opération de capture instantanée a échoué, car la limite de capture instantanée a dépassé certaines des disques attachés. Terminer les étapes et réessayez l’opération de dépannage ci-dessous.

* Supprimer les objets blob-captures instantanées des disques qui ne sont pas obligatoires. Méfiez-vous de ne pas supprimer les objets blob de disque, seuls les objets BLOB instantané doivent être supprimés.
* Si la suppression réversible est activée sur le disque de machine virtuelle comptes de stockage, configurer la rétention de suppression réversible telles que les instantanés existants sont inférieures à la valeur maximale autorisée à tout moment.
* Si Azure Site Recovery est activée dans la machine virtuelle sauvegardée, puis effectuez la ci-dessous :

    * Vérifiez la valeur de **isanysnapshotfailed** a la valeur false dans /etc/azure/vmbackup.conf
    * Planifier Azure Site Recovery à un moment différent, tel qu’il n’est pas en conflit l’opération de sauvegarde.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - opération de capture instantanée a échoué en raison de ressources de machine virtuelle inadéquates.

Code d’erreur : ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Message d’erreur : Opération de capture instantanée a échoué en raison de ressources de machine virtuelle inadéquates.

Opération de sauvegarde sur la machine virtuelle a échoué en raison de retards dans les appels réseau lors de l’exécution de l’opération de capture instantanée. Pour résoudre ce problème, effectuez l’étape 1. Si le problème persiste, essayez les étapes 2 et 3.

**Étape 1** : Créer une capture instantanée via l’hôte

À partir d’une invite de commandes avec élévation (administrateur), exécutez la commande suivante :

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Cela garantira que les captures instantanées soient effectuées via l’hôte plutôt que via l’invité. Relancez l’opération de sauvegarde.

**Étape 2** : Essayez de modifier la planification de sauvegarde à un moment où la machine virtuelle est sous une charge moindre (moins du processeur/e/s, etc..)

**Étape 3** : Essayez [augmenter la taille de machine virtuelle](https://azure.microsoft.com/blog/resize-virtual-machines/) et recommencez l’opération

## <a name="common-vm-backup-errors"></a>Erreurs de sauvegarde de la machine virtuelle courantes

| Détails de l’erreur | Solution de contournement |
| ------ | --- |
| Code d’erreur : 320001<br/> Message d’erreur : Impossible d’effectuer l’opération, car la machine virtuelle n’existe plus. <br/> <br/> Code d’erreur : 400094 <br/> Message d’erreur : La machine virtuelle n’existe pas. <br/> <br/>  Machine virtuelle Azure introuvable.  |Cette erreur se produit lorsque la machine virtuelle principale est supprimée. Cependant, la stratégie de sauvegarde continue de rechercher une machine virtuelle à sauvegarder. Pour corriger cette erreur, suivez les étapes ci-dessous : <ol><li> Recréez la machine virtuelle avec le même nom et le même nom de groupe de ressources **nom du service cloud**,<br>**ou Gestionnaire de configuration**</li><li> Arrêtez la protection de la machine virtuelle en supprimant ou non les données de sauvegarde. Pour plus d’informations, consultez [Arrêt de la protection des machines virtuelles](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| L’état d’approvisionnement de la machine virtuelle est défini sur Échec : <br>redémarrez la machine virtuelle et assurez-vous qu’elle fonctionne ou qu’elle est éteinte. | Cette erreur se produit lorsqu’un des échecs d’extension conduit à définir l’état d’approvisionnement de la machine virtuelle sur Échec. Accédez à la liste des extensions, vérifiez s’il existe une extension ayant échoué, supprimez-la et essayez de redémarrer la machine virtuelle. Si l’état de toutes les extensions est défini sur En cours d’exécution, vérifiez si le service de l’agent de machine virtuelle est en cours d’exécution. Si ce n’est pas le cas, redémarrez le service de l’agent de machine virtuelle. |
|Code d’erreur : UserErrorBCMPremiumStorageQuotaError<br/> Message d’erreur : Impossible de copier l’instantané de la machine virtuelle, en raison du manque d’espace libre dans le compte de stockage | Pour les machines virtuelles Premium sur une pile de sauvegarde de machines virtuelles V1, nous copions la capture instantanée sur le compte de stockage. Cette étape permet de s’assurer que le trafic de gestion de sauvegarde, qui fonctionne sur la capture instantanée, ne limite pas le nombre d’IOPS accessibles à l’application à l’aide de disques Premium. <br><br>Nous vous conseillons d’allouer seulement 50 pour cent (soit 17,5 To) de l’espace du compte de stockage total. Ainsi, le service Sauvegarde Azure peut copier la capture instantanée sur le compte de stockage et transférer des données depuis cet emplacement copié vers le compte de stockage dans le coffre. |
| Impossible d’installer l’extension Microsoft Recovery Services comme machine virtuelle n’est pas en cours d’exécution. <br>L’agent de machine virtuelle est une condition requise pour l’extension Recovery Services. Installez l’agent de machine virtuelle Azure, puis recommencez l’opération d’inscription. |<ol> <li>Vérifiez si l’agent de machine virtuelle a été installé correctement. <li>Vérifiez que l’indicateur de la configuration de la machine virtuelle est défini correctement.</ol> Apprenez-en plus sur l'installation de l'agent de machine virtuelle et sur la validation de cette opération. |
| L’opération de capture instantanée a échoué en renvoyant l’erreur d’opération du service Cliché instantané de volume (VSS) **Ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur à partir du panneau de configuration.** |Désactivez BitLocker pour tous les lecteurs sur la machine virtuelle et vérifiez si le problème VSS est résolu. |
| La machine virtuelle n’est pas dans un état permettant les sauvegardes. |<ul><li>Si la machine virtuelle se trouve dans un état temporaire entre **En cours d’exécution** et **Arrêt**, attendez que l’état change. Déclenchez ensuite le travail de sauvegarde. <li> Si la machine virtuelle est de type Linux et utilise le module de noyau Linux à sécurité avancée, excluez le chemin d’accès de l’agent Azure Linux **/var/lib/waagent** de la stratégie de sécurité, et assurez-vous que l’extension Sauvegarde Azure est installée.  |
| L’agent de machine virtuelle n’est pas présent sur la machine virtuelle : <br>installez les composants requis et l’agent de machine virtuelle. Ensuite, relancez l’opération. |Apprenez-en plus sur [l’installation de l’agent de machine virtuelle et la validation de cette opération](#vm-agent). |
| Sauvegarde Azure n’a pas pu figer un ou plusieurs points de montage de la machine virtuelle pour prendre une capture instantanée cohérente au niveau du système de fichiers. | Procédez comme suit : <ul><li>Vérifiez l’état du système de fichiers de tous les périphériques montés avec la commande **’tune2fs’** . Par exemple, **tune2fs -l/dev/sdb1 \\** .\| grep **Filesystem state**. <li>Démontez les périphériques pour lesquels l’état du système de fichiers n’est pas propre à l’aide de la commande **’umount’** . <li> Exécutez une vérification de cohérence de système de fichiers sur ces périphériques à l’aide de la commande **’fsck’** . <li> Remontez les périphériques et tentez une sauvegarde.</ol> |
| Échec de l’opération de capture instantanée en raison de l’échec de la création du canal de communication réseau sécurisé. | <ol><li> Ouvrez l’Éditeur du Registre en exécutant **regedit.exe** avec élévation de privilèges. <li> Identifiez toutes les versions de. NET Framework présentes dans votre système. Elles se trouvent dans la hiérarchie de la clé de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Pour chaque .NET Framework présent dans la clé de Registre, ajoutez la clé suivante : <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| Échec de l’opération de capture instantanée en raison de l’échec de l’installation de Redistribuable Visual C++ pour Visual Studio 2012. | Accédez à C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion et installez vcredist2012_x64.<br/>Assurez-vous que la valeur de clé de Registre qui permet l’installation du service est définie à la valeur correcte. Autrement dit, définissez le **Démarrer** valeur dans **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** à **3** et non **4**. <br><br>Si vous rencontrez toujours des problèmes d’installation, redémarrez le service d’installation en exécutant **MSIEXEC /UNREGISTER** suivi de **MSIEXEC /REGISTER** dans une invite de commandes avec élévation de privilèges.  |


## <a name="jobs"></a>Tâches

| Détails de l’erreur | Solution de contournement |
| --- | --- |
| L’annulation n’est pas prise en charge pour ce type de travail : <br>Attendez que le travail se termine. |Aucun |
| Le travail n’est pas dans un état annulable : <br>Attendez que le travail se termine. <br>**ou Gestionnaire de configuration**<br> Le travail sélectionné n’est pas dans un état annulable : <br>Attendez que le travail se termine. |Il est probable que le travail soit presque terminé. Attendez que le travail se termine.|
| Sauvegarde Azure ne peut pas annuler le travail, car il n’est pas en cours d’exécution : <br>L’annulation est uniquement prise en charge pour les travaux en cours. Essayez d’annuler un travail en cours d’exécution. |Cette erreur se produit en raison d’un état temporaire. Attendez une minute et relancez l’opération d’annulation. |
| Sauvegarde Azure n’a pas annulé le travail : <br>Attendez que le travail se termine. |Aucun |

## <a name="restore"></a>Restore

| Détails de l’erreur | Solution de contournement |
| --- | --- |
| Échec de la restauration avec une erreur interne du cloud. |<ol><li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec des paramètres DNS. Vous pouvez vérifier : <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Si **Adresse** est configuré, les paramètres DNS sont configurés.<br> <li>Le service cloud sur lequel vous tentez d’effectuer la restauration est configuré avec une **adresse IP réservée**, et les machines virtuelles existantes dans le service cloud sont à l’état arrêté. Vous pouvez vérifier qu’un service cloud a réservé une adresse IP à l’aide des cmdlets PowerShell suivantes : **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Vous essayez de restaurer une machine virtuelle avec les configurations réseau spéciales suivantes dans le même service cloud : <ul><li>Machines virtuelles avec configuration d’un équilibreur de charge, internes et externes.<li>Machines virtuelles avec plusieurs adresses IP réservées. <li>Machines virtuelles avec plusieurs NIC. </ul><li>Sélectionnez un nouveau service cloud dans l’interface utilisateur ou consultez les [considérations relatives à la restauration](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) des machines virtuelles avec des configurations réseau spéciales.</ol> |
| Le nom DNS sélectionné est déjà attribué : <br>Spécifiez un autre nom DNS, puis réessayez. |Ce nom DNS fait référence au nom du service cloud, qui se termine généralement par **.cloudapp.net**. Ce nom doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom de machine virtuelle pendant la restauration. <br><br> Cette erreur ne s’affiche que pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell se déroule correctement, car elle ne fait que restaurer les disques et ne crée pas de machine virtuelle. L’erreur se rencontre lorsque la machine virtuelle est explicitement créée par vos soins après l’opération de restauration du disque. |
| La configuration de réseau virtuel spécifiée n’est pas correcte : <br>spécifiez une autre configuration de réseau virtuel et réessayez. |Aucun |
| Le service cloud spécifié utilise une adresse IP réservée qui ne correspond pas à la configuration de la machine virtuelle en cours de restauration : <br>spécifiez un autre service cloud qui n’utilise pas d’adresse IP réservée. Ou choisissez un autre point de restauration à partir duquel effectuer la restauration. |Aucun |
| Le service cloud a atteint sa limite sur le nombre de points de terminaison d’entrée : <br>relancez l’opération en spécifiant un autre service cloud ou en utilisant un point de terminaison existant. |Aucun |
| Le compte de stockage cible et le coffre Recovery Services se trouvent dans deux régions distinctes : <br>vérifiez que le compte de stockage spécifié dans l’opération de restauration se trouve dans la même région Azure que votre coffre Recovery Services. |Aucun |
| Le compte de stockage spécifié pour l’opération de restauration n’est pas pris en charge : <br>Seuls les comptes de stockage De base ou Standard avec des paramètres de réplication géoredondants ou redondants localement sont pris en charge. Sélectionnez un compte de stockage pris en charge. |Aucun |
| Le type de compte de stockage spécifié pour l’opération de restauration n’est pas en ligne : <br>Vérifiez que le compte de stockage spécifié dans l’opération de restauration est en ligne. |Cette erreur peut se produire dans le cas d’une erreur temporaire dans Stockage Azure ou d’une panne. Choisissez un autre compte de stockage. |
| Le quota du groupe de ressources a été atteint : <br>Supprimez des groupes de ressources à partir du Portail Azure ou contactez le support Azure pour augmenter les limites. |Aucun |
| Le sous-réseau sélectionné n’existe pas : <br>Sélectionnez un sous-réseau qui existe. |Aucun |
| Le service Sauvegarde Azure n’a pas l’autorisation d’accéder aux ressources dans votre abonnement. |Pour résoudre cette erreur, commencez par restaurer les disques à l’aide de la procédure décrite dans [Restaurer des disques sauvegardés](backup-azure-arm-restore-vms.md#restore-disks). Utilisez ensuite les étapes PowerShell indiquées dans [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Sauvegarde ou restauration qui prend du temps
Si votre sauvegarde prend plus de 12 heures, ou si la restauration prend plus de 6 heures, passez en revue les [meilleures pratiques](backup-azure-vms-introduction.md#best-practices) et les [considérations relatives aux performances](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agent VM
### <a name="set-up-the-vm-agent"></a>Configurer l’agent de machine virtuelle
En règle générale, l’agent de machine virtuelle est déjà présent dans les machines virtuelles qui sont créées à partir de la galerie Azure. Cependant, les machines virtuelles qui sont migrées à partir de centres de données locaux n’ont pas d’agent de machine virtuelle installé. Pour ces machines virtuelles, l’agent de machine virtuelle doit être installé de manière explicite.

#### <a name="windows-vms"></a>Machines virtuelles Windows

* Téléchargez et installez le fichier [MSI de l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges Administrateur pour terminer l’installation.
* Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, [mettez à jour la propriété de la machine virtuelle](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. Cette étape n’est pas requise pour les machines virtuelles Azure Resource Manager.

#### <a name="linux-vms"></a>Machines virtuelles Linux

* Installez la dernière version de l’agent à partir du référentiel de distribution. Pour plus de d’informations sur le nom du package, consultez le [référentiel de l’agent Linux](https://github.com/Azure/WALinuxAgent).
* Pour les machines virtuelles créées à l’aide du modèle de déploiement classique, [consultez ce blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour mettre à jour la propriété de la machine virtuelle et vérifiez que l’agent est installé. Cette étape n’est pas requise pour les machines virtuelles du Gestionnaire des ressources.

### <a name="update-the-vm-agent"></a>Mettre à jour l’agent de machine virtuelle
#### <a name="windows-vms"></a>Machines virtuelles Windows

* Pour mettre à jour l’agent de machine virtuelle, réinstallez les [fichiers binaires de l’agent de machine virtuelle](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Avant de mettre à jour l’agent, assurez-vous qu’aucune opération de sauvegarde ne se déroule pendant la mise à jour de l’agent de machine virtuelle.

#### <a name="linux-vms"></a>Machines virtuelles Linux

* Pour mettre à jour l’agent de machine virtuelle Linux, suivez les instructions de l’article [Guide pratique pour mettre à jour l’agent Linux Azure sur une machine virtuelle](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Utilisez toujours le référentiel de distribution pour mettre à jour l’agent.

    Ne téléchargez pas le code de l’agent depuis GitHub. Si le dernier agent n’est pas disponible pour votre distribution, contactez le support de distribution pour savoir comment obtenir l’agent le plus récent. Vous pouvez également consulter les dernières informations sur [l’agent Linux de Windows Azure](https://github.com/Azure/WALinuxAgent/releases) dans le référentiel GitHub.

### <a name="validate-vm-agent-installation"></a>Valider l’installation de l’agent de machine virtuelle

Vérifiez la version de l’agent de machine virtuelle sur les machines virtuelles Windows :

1. Connectez-vous à la machine virtuelle Azure et accédez au dossier **C:\WindowsAzure\Packages**. Vous devez trouver le fichier **WaAppAgent.exe**.
2. Cliquez avec le bouton droit sur le fichier et accédez à **Propriétés**. Sélectionnez ensuite l’onglet **Détails**. Le champ **Version du produit** doit indiquer 2.6.1198.718 ou une version ultérieure.

## <a name="troubleshoot-vm-snapshot-issues"></a>Résoudre les problèmes de capture instantanée de machine virtuelle
La sauvegarde de machines virtuelles émet des commandes de capture instantanée à destination du stockage sous-jacent. Le fait de ne pas avoir accès au stockage ou tout retard dans l’exécution d’une tâche de capture instantanée peut faire échouer le travail de sauvegarde. Voici les causes possibles de l’échec d’une tâche de capture instantanée :

- **Accès réseau au Stockage bloqué par l’utilisation du groupe de sécurité réseau**. En savoir plus sur comment [établir l’accès réseau](backup-azure-arm-vms-prepare.md#establish-network-connectivity) vers le stockage à l’aide d’une liste autorisée des adresses IP ou via un serveur proxy.
- **Les machines virtuelles pour lesquelles la sauvegarde SQL Server est configurée peuvent provoquer des retards de tâches de capture instantanée**. Par défaut, la sauvegarde de machines virtuelles crée une sauvegarde complète VSS sur les machines virtuelles Windows. Les machines virtuelles qui exécutent SQL Server, avec la sauvegarde SQL Server configurée, peuvent subir des retards dans les captures instantanées. Si des retards dans les captures instantanées font échouer la sauvegarde, définissez la clé de Registre suivante :

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **L’état de la machine virtuelle est rapporté de manière incorrecte en raison de l’arrêt de la machine virtuelle dans RDP**. Si vous avez arrêté la machine virtuelle à l’aide du bureau distant, vérifiez que l’état de la machine virtuelle dans le portail est correct. Si l’état est incorrect, utilisez l’option **Arrêt** dans le tableau de bord de la machine virtuelle du portail pour éteindre la machine virtuelle.
- **Si plus de quatre machines virtuelles partagent le même service cloud, répartissez les machines virtuelles sur plusieurs stratégies de sauvegarde**. Échelonnez les heures de sauvegarde de façon que quatre sauvegardes de machine virtuelle maximum ne démarrent pas en même temps. Essayez de décaler d’au moins une heure les démarrages d’une stratégie à une autre.
- **La machine virtuelle s’exécute avec un niveau de mémoire ou d’UC élevé**. Si la machine virtuelle sollicite fortement la mémoire ou le processeur (plus de 90 pour cent), votre tâche de capture instantanée est mise en file d’attente et retardée. Elle peut éventuellement arriver à expiration. Si ce problème se produit, essayez de procéder à une sauvegarde à la demande.

## <a name="networking"></a>Mise en réseau
Comme toutes les extensions, l’extension Sauvegarde Azure a besoin d’accéder à l’Internet public pour fonctionner. En l’absence d’accès Internet public, plusieurs cas de figure sont possibles :

* L’installation de l’extension peut échouer.
* Les opérations de sauvegarde telles que la capture instantanée de disque peuvent échouer.
* L’affichage de l’état de l’opération de sauvegarde peut échouer.

La nécessité de résoudre les adresses Internet publiques est abordée dans [ce blog du support Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vérifiez la configuration DNS du réseau virtuel et assurez-vous que les URI Azure peuvent être résolus.

Une fois que la résolution de noms a été effectuée correctement, l’accès aux adresses IP Azure doit également être fourni. Pour débloquer l’accès à l’infrastructure Azure, effectuez une des opérations suivantes :

- Autoriser la liste des plages IP de centre de données Azure :
   1. Obtenir la liste des [adresses IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour être en liste verte.
   1. Débloquez les adresses IP à l’aide de la cmdlet [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute). Exécutez cette cmdlet dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges. Exécutez en tant qu’Administrateur.
   1. Ajoutez des règles au groupe de sécurité réseau, le cas échéant, pour autoriser l’accès aux adresses IP.
- Créez un chemin d’accès pour le trafic HTTP :
   1. Si vous avez des restrictions réseau en place, déployez un serveur proxy HTTP pour acheminer le trafic. Par exemple, un groupe de sécurité réseau. Consultez la procédure de déploiement d’un serveur proxy HTTP dans [Établir la connectivité réseau](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Ajoutez des règles au groupe de sécurité réseau, le cas échéant, pour autoriser l’accès à Internet à partir du proxy HTTP.

> [!NOTE]
> Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne. Si vous avez besoin d’une adresse IP privée statique, configurez-la via le Portail Azure ou PowerShell. Vérifiez que l’option DHCP à l’intérieur de la machine virtuelle est activée.
> Pour obtenir plus d’informations sur la configuration d’une adresse IP statique via PowerShell :
> - [Ajout d’une adresse IP interne statique à une machine virtuelle existante](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Modifier la méthode d’allocation pour une adresse IP privée affectée à une interface réseau](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
