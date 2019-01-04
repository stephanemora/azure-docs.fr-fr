---
title: À propos du service Mobilité pour la reprise d'activité des serveurs physiques et des machines virtuelles VMware à l'aide d'Azure Site Recovery | Microsoft Docs
description: Familiarisez-vous avec l'agent du service Mobilité pour la reprise d’activité des serveurs physiques et des machines virtuelles VMware sur Azure à l’aide du service Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: e3967319cd41399209bd50886bce88efc8ba6ba6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956514"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>À propos du service Mobilité pour serveurs physiques et machines virtuelles VMware

Lorsque vous configurez la reprise d’activité des serveurs physiques et des machines virtuelles VMware à l'aide d'[Azure Site Recovery](site-recovery-overview.md), vous installez le service Mobilité de Site Recovery sur l'ensemble des serveurs physiques et des machines virtuelles VMware locaux.  Le service Mobilité enregistre les écritures de données sur la machine et les transmet au serveur de processus Site Recovery. Vous pouvez déployer le service Mobilité à l’aide des méthodes suivantes :

[Installation Push](vmware-azure-install-mobility-service.md) : configurez Site Recovery pour qu’il procède à une installation Push du service Mobilité. Pour ce faire, quand vous configurez la reprise d’activité, vous configurez également un compte que le serveur de traitement Site Recovery pourra utiliser pour accéder à la machine virtuelle ou au serveur physique dans le cadre de l’installation du service.
[Installation manuelle](vmware-physical-mobility-service-install-manual.md) : vous pouvez installer le service Mobilité manuellement sur chaque machine à l’aide de l’interface utilisateur ou d’une invite de commandes.
[Déploiement automatisé](vmware-azure-mobility-install-configuration-mgr.md) : vous pouvez automatiser l’installation à l’aide d’outils de déploiement de logiciels tels que System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Agent de machine virtuelle Azure

- **Machines virtuelles Windows** : à partir de la version 9.7.0.0 du service Mobilité, l’[agent de machine virtuelle Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) est installé par le programme d’installation du service Mobilité. Ainsi, lorsque la machine bascule vers Azure, la machine virtuelle Azure remplit les conditions préalables à l’installation de l'agent pour utiliser une extension de machine virtuelle.
- **Machines virtuelles Linux** : l’agent [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) doit être installé manuellement sur la machine virtuelle Azure après le basculement.

## <a name="installer-files"></a>Fichiers d’installation

Le tableau récapitule les fichiers d’installation du système d’exploitation de chaque serveur physique et machine virtuelle VMware. N'hésitez pas à consulter les [systèmes d’exploitation pris en charge](vmware-physical-azure-support-matrix.md#replicated-machines) avant de commencer.


**Fichier d’installation** | **Système d’exploitation (64 bits uniquement)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016 ; Windows Server 2012 R2; Windows Server 2012 ; Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serveur Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7 64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>Protection antivirus sur les machines répliquées

Si les machines que vous souhaitez répliquer ont un logiciel antivirus actif en cours d'exécution, assurez-vous d'exclure le dossier d'installation du service Mobilité des opérations antivirus (*C:\ProgramData\ASR\agent*). Cela garantit le bon fonctionnement de la réplication.

## <a name="update-the-mobility-service"></a>Mettre à jour le service Mobilité

1. Avant de commencer, veillez à ce que le serveur de configuration, les serveurs de processus de scale-out et les serveurs cibles maîtres qui font partie de votre déploiement soient mis à jour avant de procéder à la mise à jour du service Mobilité sur les machines protégées.
2. Sur le portail, ouvrez le coffre > **Éléments répliqués**.
3. Si le serveur de configuration correspond à la dernière version, une notification doit s’afficher et indiquer « Une nouvelle mise à jour de l’agent de réplication Site Recovery est disponible. Cliquez pour installer. »

     ![Fenêtre Éléments répliqués](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Cliquez sur la notification, puis, dans **Mise à jour de l’agent**, sélectionnez les machines sur lesquelles vous souhaitez mettre à niveau le service Mobilité. Cliquez ensuite sur **OK**.

     ![Éléments répliqués - Liste des machines virtuelles](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. La tâche Mettre à jour le service Mobilité est alors lancée pour chacune des machines sélectionnées.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Mettre à jour le compte utilisé pour l’installation Push du service Mobilité

Au moment du déploiement de Site Recovery, pour activer l’installation Push du service Mobilité, vous avez spécifié un compte que le serveur de processus Site Recovery utilise afin d'accéder aux machines et installer le service lorsque de la réplication est activée pour la machine. Pour mettre à jour les informations d’identification de ce compte, suivez [ces instructions](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Désinstaller le service Mobilité

### <a name="on-a-windows-machine"></a>Sur une machine Windows

Désinstallez le service à partir de l’interface utilisateur ou d’une invite de commandes.

- **À partir de l’interface utilisateur** : dans le Panneau de configuration de la machine, sélectionnez **Programmes**. Sélectionnez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery** > **Désinstaller**.
- **À partir d’une invite de commandes** : sur la machine, ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Exécutez la commande suivante : 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Sur une machine Linux
1. Sur la machine Linux, connectez-vous en tant qu’utilisateur **racine**.
2. Dans un terminal, accédez à /user/local/ASR.
3. Exécutez la commande suivante :

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Étapes suivantes

[Configurer l’installation Push du service Mobilité](vmware-azure-install-mobility-service.md)
