---
title: Détecter un problème lié aux agents et aux extensions
description: Symptômes, causes et résolution des défaillances de la Sauvegarde Azure liées à l’agent, à l’extension et aux disques.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: c4ee8cbeeec21c4af0cc3a7fd83844bc8c676add
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172605"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Résoudre les problèmes d’une Sauvegarde Azure : Problèmes d’agent ou d’extension

Cet article indique les étapes à suivre pour résoudre les erreurs de la Sauvegarde Azure liées à la communication avec l’agent et l’extension de machine virtuelle.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable : L’agent de machine virtuelle ne peut pas communiquer avec le service Sauvegarde Azure

**Code d’erreur** : UserErrorGuestAgentStatusUnavailable <br>
**Message d’erreur** : L’agent de machine virtuelle ne peut pas communiquer avec la sauvegarde Microsoft Azure<br>

L’agent de machine virtuelle Azure peut être arrêté, obsolète, dans un état incohérent ou non installé et empêcher le service Sauvegarde Azure de déclencher des instantanés.  

- Si l’agent de machine virtuelle est arrêté ou est dans un état incohérent, **redémarrez l’agent** et réessayez l’opération de sauvegarde (essayez une sauvegarde sur demande). Pour connaître les étapes de redémarrage de l’agent, consultez [Machines virtuelles Windows](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) ou [Machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Si l’agent de machine virtuelle n’est pas installé ou est obsolète, installez/mettez à jour l’agent de machine virtuelle et réessayez l’opération de sauvegarde. Pour connaître les étapes d’installation/de mise à jour de l’agent, consultez [Machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError : Impossible de communiquer avec l’agent de machine virtuelle pour obtenir l’état de l’instantané

**Code d’erreur** : GuestAgentSnapshotTaskStatusError<br>
**Message d’erreur** : Impossible de communiquer avec l’agent en charge de la machine virtuelle pour avoir un aperçu de l’état instantané <br>

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  

**Cause 1 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Cause 2 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Cause 3 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Cause 4 : [L’extension de sauvegarde ne peut être ni mise à jour ni chargée](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached : Limite maximale de la collection de points de restauration atteinte

**Code d’erreur** : UserErrorRpCollectionLimitReached <br>
**Message d’erreur** : La limite maximale de la collection de points de restauration est atteinte. <br>

- Ce problème peut se produire si un verrou dans le groupe de ressources des points de récupération empêche le nettoyage automatique des points de récupération.
- Ce problème peut également se produire si plusieurs sauvegardes sont déclenchées tous les jours. Actuellement, nous recommandons une seule sauvegarde par jour, car les points de restauration instantanés sont conservés pendant 1 à 5 jours conformément à la rétention d’instantanés configurée, et seuls 18 points de restauration instantanés peuvent être associés à une machine virtuelle à un moment donné. <br>

Action recommandée :<br>
Pour résoudre ce problème, supprimez le verrou du groupe de ressources de la machine virtuelle et recommencez l’opération pour déclencher le nettoyage.
> [!NOTE]
> Le service de sauvegarde crée un groupe de ressources distinct du groupe de ressources de la machine virtuelle afin de stocker la collection de points de restauration. Les clients sont invités à ne pas verrouiller le groupe de ressources créé pour une utilisation par le service de sauvegarde. Le format d’affectation des noms du groupe de ressources créé par le service de sauvegarde est : AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Étape 1 : [Supprimer le verrou du groupe de ressources des points de restauration](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Étape 2 : [ Nettoyer la collection de points de restauration](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured : Sauvegarde Azure ne dispose pas des autorisations d’accès suffisantes au coffre de clés pour la sauvegarde de machines virtuelles chiffrées

**Code d’erreur** : UserErrorKeyvaultPermissionsNotConfigured <br>
**Message d’erreur** : Sauvegarde Azure ne dispose pas des autorisations d’accès suffisantes au coffre de clés pour la sauvegarde de machines virtuelles chiffrées. <br>

Pour qu’une opération de sauvegarde puisse réussir sur des machines virtuelles chiffrées, elle doit avoir les autorisations d’accès au coffre de clés. Cela est possible par l’intermédiaire du [portail Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou de [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork : L’opération de capture instantanée a échoué, car la machine virtuelle ne présente aucune connectivité réseau

**Code d’erreur** : ExtensionSnapshotFailedNoNetwork<br>
**Message d’erreur** : L’opération de capture instantanée a échoué, car la machine virtuelle ne présente aucune connectivité réseau<br>

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :

**Cause 1 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 2 : [L’extension de sauvegarde ne peut être ni mise à jour ni chargée](#the-backup-extension-fails-to-update-or-load)**  
**Cause 3 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks : l’opération d’extension VMSnapshot a échoué

**Code d’erreur** : ExtensionOperationFailedForManagedDisks <br>
**Message d’erreur** : Échec de l’opération d’extension VMSnapshot<br>

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  
**Cause 1 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 2 : [L’extension de sauvegarde ne peut être ni mise à jour ni chargée](#the-backup-extension-fails-to-update-or-load)**  
**Cause 3 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Cause 4 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 : La sauvegarde échoue, avec une erreur interne

**Code d’erreur** : BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Message d’erreur** : Échec de l’opération de sauvegarde avec une erreur interne : veuillez retenter l’opération dans quelques minutes <br>

Après avoir enregistré et planifié une machine virtuelle pour le service Azure Backup , ce dernier lance le travail en communiquant avec l’extension de sauvegarde de la machine virtuelle pour prendre un instantané à un moment donné. Il est possible que l’une des conditions suivantes empêche le déclenchement de l’instantané. Si la capture instantanée n’est pas déclenchée, un échec de sauvegarde risque de se produire. Suivez les étapes de dépannage ci-dessous dans l’ordre indiqué, puis réessayez l’opération :  
**Cause 1 : [L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Cause 2 : [L’agent installé dans la machine virtuelle est obsolète (machines virtuelles Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Cause 3 : [Impossible de récupérer l’état de l’instantané ou de capturer un instantané](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Cause 4 : [L’extension de sauvegarde ne peut être ni mise à jour ni chargée](#the-backup-extension-fails-to-update-or-load)**  
**Cause 5 : Le service de sauvegarde n’est pas autorisé à supprimer les anciens points de restauration en raison du verrouillage d’un groupe de ressources** <br>
**Cause 6 : [La machine virtuelle n’a pas accès à Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize : la taille de disque configurée n’est pas prise en charge par Sauvegarde Azure.

**Code d’erreur** : UserErrorUnsupportedDiskSize <br>
**Message d’erreur** : La taille de disque configurée n’est pas prise en charge par Sauvegarde Azure. <br>

Votre opération de sauvegarde peut échouer lors de la sauvegarde d’une machine virtuelle dont la taille de disque est supérieure à 32 To. De plus, la sauvegarde de disques chiffrés d’une taille supérieure à 4 To n’est pas prise en charge actuellement. Vérifiez que la taille de disque est inférieure ou égale à la limite prise en charge en fractionnant les disques.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Impossible de lancer la sauvegarde, car une autre opération de sauvegarde est en cours d’exécution

**Code d’erreur** : UserErrorBackupOperationInProgress <br>
**Message d’erreur** : Impossible de lancer la sauvegarde, car une autre opération de sauvegarde est en cours d’exécution<br>

L’une de vos dernières tâches de sauvegarde a échoué, car il y a déjà une tâche de sauvegarde en cours. Vous ne pouvez pas en lancer de nouvelle tant que la tâche en cours n’est pas terminée. Vérifiez que l’opération de sauvegarde en cours est terminée avant d’en déclencher ou d’en programmer d’autres. Pour vérifier le statut des tâches de sauvegarde, suivez les étapes ci-dessous :

1. Connectez-vous au portail Azure et cliquez sur **Tous les services**. Tapez Recovery Services, puis cliquez sur **Coffres Recovery Services**. La liste des coffres Recovery Services s’affiche.
2. Dans la liste des coffres Recovery Services, sélectionnez un coffre dont la sauvegarde est configurée.
3. Dans le menu du tableau de bord du coffre, cliquez sur **Tâches de sauvegarde** pour afficher toutes les tâches de sauvegarde.
   - Si une tâche de sauvegarde est en cours, attendez qu’elle se termine ou annulez-la.
     - Pour annuler la tâche de sauvegarde, cliquez dessus avec le bouton droit, puis cliquez sur **Annuler** ou utilisez [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Si vous avez reconfiguré la sauvegarde dans un autre coffre, vérifiez qu’aucune tâche de sauvegarde n’est en cours d’exécution dans l’ancien coffre. S’il y en a une, annulez-la.
     - Pour annuler le travail de sauvegarde, cliquez dessus avec le bouton droit de la souris, puis cliquez sur **Annuler** ou utilisez [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Réessayez l’opération de sauvegarde.

Si l’opération de sauvegarde planifiée empiète sur la configuration de la sauvegarde suivante, consultez [Bonnes pratiques](backup-azure-vms-introduction.md#best-practices), [Performances de sauvegarde](backup-azure-vms-introduction.md#backup-performance) et [Considérations sur la restauration](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="causes-and-solutions"></a>Causes et solutions

### <a name="the-vm-has-no-internet-access"></a>La machine virtuelle n’a pas accès à Internet

Selon la spécification du déploiement, la machine virtuelle n’a pas accès à Internet. Il se peut également que des restrictions empêchent l’accès à l’infrastructure Azure.

Pour fonctionner correctement, l’extension Sauvegarde a besoin d’une connectivité aux adresses IP publiques Azure. Elle envoie des commandes à un point de terminaison Stockage Azure (URL HTTPs) pour gérer les instantanés de la machine virtuelle. Si elle n’a pas accès à l’Internet public, la sauvegarde échoue.

#### <a name="solution"></a>Solution

Pour résoudre le problème de réseau, reportez-vous à [Établir la connectivité réseau](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L’agent est installé dans la machine virtuelle, mais ne répond pas (machines virtuelles Windows)

#### <a name="solution"></a>Solution

Il se peut que l’agent de machine virtuelle soit endommagé ou que le service ait été arrêté. Réinstallez l’agent de machine virtuelle pour obtenir la dernière version. Cela permet également de redémarrer la communication avec le service.

1. Regardez si le service d’agent invité Microsoft Azure s’exécute dans les services de machine virtuelle (services.msc). Essayez de le redémarrer et de lancer la sauvegarde.
2. Si le service d’agent invité Windows Azure n’apparaît pas dans les services, accédez à **Programmes et fonctionnalités** dans le Panneau de configuration pour déterminer s’il est installé.
3. Si le service d’agent invité Wondows Azure figure sous **Programmes et fonctionnalités**, désinstallez-le.
4. Téléchargez et installez la [dernière version du MSI de l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Des droits d’administrateur sont nécessaires pour effectuer l’installation.
5. Vérifiez que le service d’agent invité Microsoft Azure apparaît dans les services.
6. Exécutez une sauvegarde à la demande :
   - Dans le portail, sélectionnez **Sauvegarder maintenant**.

Vérifiez également que [Microsoft .NET 4.5 est installé](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) dans la machine virtuelle. Si ce n’est pas le cas, l’agent de machine virtuelle ne pourra pas communiquer avec le service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L’agent installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)

#### <a name="solution"></a>Solution

La plupart des échecs des machines virtuelles Linux liés aux agents ou aux extensions sont causés par des problèmes qui affectent un agent obsolète de machine virtuelle. Pour résoudre ce problème, suivez ces recommandations générales :

1. Suivez les instructions fournies pour la [mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Nous *recommandons vivement* la mise à jour de l’agent uniquement par le biais de référentiel de distribution. Nous ne recommandons pas de télécharger le code de l’agent à partir de GitHub directement et d’effectuer la mise à jour. Si la dernière version de l’agent n’est pas disponible pour la distribution, contactez le support de distribution pour savoir comment l’installer. Pour rechercher l’agent le plus récent, accédez à la page relative à l’[agent Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) du référentiel GitHub.

2. Vérifiez que l’agent Azure s’exécute sur la machine virtuelle à l’aide de la commande suivante : `ps -e`.

   Si le processus ne s’exécute pas, redémarrez-le à l’aide des commandes suivantes :

   - Pour Ubuntu : `service walinuxagent start`
   - Pour les autres distributions : `service waagent start`

3. [Configurez l’agent de redémarrage automatique](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Exécutez une nouvelle sauvegarde de test. Si la défaillance persiste, collectez les journaux d’activité suivants à partir de la machine virtuelle :

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Si vous exigez une journalisation détaillée pour waagent, procédez comme suit :

1. Dans le fichier /etc/waagent.conf, recherchez la ligne suivante : **Activer l’enregistrement des informations détaillées (y|n)**
2. Passez la valeur **Logs.Verbose** de *N* à *O*.
3. Enregistrez la modification, puis redémarrez waagent en suivant les étapes détaillées plus haut dans cette section.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Impossible de récupérer l’état de l’instantané ou de capturer un instantané

La sauvegarde de machine virtuelle émet une commande de capture instantanée à destination du stockage sous-jacent. Elle peut échouer pour deux raisons : soit elle n’a pas accès au compte de stockage, soit l’exécution de la tâche de capture instantanée est différée.

#### <a name="solution"></a>Solution

Voici les causes possibles de l’échec de la tâche de capture instantanée :

| Cause : | Solution |
| --- | --- |
| L’état de la machine virtuelle est rapporté de manière incorrecte, car la machine virtuelle est arrêtée dans le protocole RDP (Remote Desktop Protocol). | Si vous avez arrêté la machine virtuelle dans le protocole RDP, retournez sur le portail pour vérifier que son état est correct. Si ce n’est pas le cas, arrêtez la machine virtuelle dans le portail à l’aide de l’option **Arrêter** dans le tableau de bord de la machine virtuelle. |
| La machine virtuelle ne parvient pas à récupérer l’adresse d’hôte/de structure à partir du protocole DHCP. | Le protocole DHCP doit être activé dans l’invité pour que la sauvegarde de la machine virtuelle IaaS fonctionne. Si la machine virtuelle ne parvient pas à récupérer l’adresse d’hôte/de structure à partir de la réponse 245 DHCP, elle ne peut ni télécharger, ni exécuter des extensions. Si vous avez besoin d’une adresse IP privée statique, vous devez la configurer via le **portail Azure** ou **PowerShell** et vérifier que l’option DHCP dans la machine virtuelle est activée. [Apprenez-en davantage](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sur la configuration d’une adresse IP statique avec PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>L’extension de sauvegarde ne peut être ni mise à jour ni chargée

Si les extensions ne sont pas chargées, la sauvegarde échoue, car il n’est pas possible de capturer un instantané.

#### <a name="solution"></a>Solution

Désinstallez l’extension pour forcer le rechargement de l’extension VMSnapshot. La prochaine tentative de sauvegarde rechargera l’extension.

Pour désinstaller l’extension :

1. Sur le [Portail Azure](https://portal.azure.com/), accédez à la machine virtuelle qui rencontre des échecs de sauvegarde.
2. Sélectionnez **Paramètres**.
3. Sélectionnez **Extensions**.
4. Sélectionnez **Extension VMSnapshot**.
5. Sélectionnez **Désinstaller**.

Pour les machines virtuelles Linux, si l’extension VMSnapshot ne figure pas dans le portail Azure, [mettez à jour l’agent Linux Azure](../virtual-machines/linux/update-agent.md), puis exécutez la sauvegarde.

Cette procédure réinstalle l’extension lors de la sauvegarde suivante.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Supprimer le verrou du groupe de ressources des points de récupération

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez l’option **Toutes les ressources**, puis le groupe de ressources de la collection de points de restauration au format suivant : AzureBackupRG_`<Geo>`_`<number>`.
3. Dans la section **Paramètres**, sélectionnez **Verrous** pour afficher les verrous.
4. Pour supprimer le verrou, sélectionnez les points de suspension, puis cliquez sur **Supprimer**.

    ![Supprimer un verrou](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Nettoyer la collection de points de restauration

Après avoir supprimé le verrou, vous devez nettoyer les points de restauration.

Si vous supprimez le groupe de ressources de la machine virtuelle, voire la machine virtuelle elle-même, les instantanés de restauration des disques managés restent actifs et expirent en fonction de la conservation définie. Pour supprimer les instantanés de restauration (dont vous n'avez plus besoin) stockés dans la collection de points de restauration, nettoyez cette dernière en suivant la procédure indiquée ci-dessous.

Pour nettoyer les points de restauration, suivez l’une des méthodes suivantes :<br>

- [Nettoyer la collection de points de restauration en exécutant une sauvegarde sur demande](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Nettoyer la collection de points de restauration sur le portail Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Nettoyer la collection de points de restauration en exécutant une sauvegarde sur demande

Après avoir supprimé le verrou, déclenchez une sauvegarde sur demande. Ce faisant, les points de restauration sont automatiquement nettoyés. Attendez-vous à ce que cette opération sur demande échoue la première fois. Toutefois, elle garantit le nettoyage automatique à la place de la suppression manuelle des points de restauration. La sauvegarde ultérieure planifiée doit réussir après le nettoyage.

> [!NOTE]
> Le nettoyage automatique a lieu quelques heures après le déclenchement de la sauvegarde sur demande. Si votre sauvegarde planifiée échoue à nouveau, essayez de supprimer manuellement la collection de points de restauration à l’aide de la procédure indiquée [ici](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Nettoyer la collection de points de restauration sur le portail Azure <br>

Pour effacer manuellement la collection des points de restauration qui ne sont pas effacés en raison du verrou du groupe de ressources, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu **Hub**, cliquez sur **Toutes les ressources**, sélectionnez le groupe de ressources au format suivant AzureBackupRG_`<Geo>`_`<number>` où se trouve votre machine virtuelle.

    ![Supprimer un verrou](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Cliquez sur le groupe de ressources ; le panneau **Vue d’ensemble** s’affiche.
4. Sélectionnez l’option **Afficher les types masqués** pour afficher toutes les ressources masquées. Sélectionnez les collections de points de restauration au format suivant : AzureBackupRG_`<VMName>`_`<number>`.

    ![Supprimer un verrou](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Cliquez sur **Supprimer** pour nettoyer la collection des points de restauration.
6. Renouvelez l’opération de sauvegarde.

> [!NOTE]
 >Si la ressource (RP Collection) compte un grand nombre de points de restauration, leur suppression du portail peut entraîner un dépassement de temps et un échec. Il s’agit d’un problème CRP connu, où tous les points de restauration ne sont pas supprimés dans le temps imparti, ce qui entraîne l’expiration du délai d’attente de l’opération ; cependant, l’opération de suppression réussit généralement après 2 ou 3 tentatives.
