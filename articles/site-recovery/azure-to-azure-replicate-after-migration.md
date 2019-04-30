---
title: Configurer la récupération d’urgence pour des machines virtuelles Azure après la migration vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment préparer des machines pour configurer la récupération d’urgence entre des régions Azure après une migration vers Azure à l’aide d’Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 019c6ec776277a9102cb95cd685bbae0fc660d66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789686"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurer la récupération d’urgence pour des machines virtuelles Azure après la migration vers Azure 


Suivez cet article si vous avez [migrés sur des ordinateurs locaux aux machines virtuelles Azure](tutorial-migrate-on-premises-to-azure.md) à l’aide de la [Site Recovery](site-recovery-overview.md) service et que vous souhaitez maintenant obtenir les machines virtuelles définies pour la récupération d’urgence vers une région Azure secondaire. L’article décrit comment s’assurer que l’agent de machine virtuelle Azure est installé sur les machines virtuelles migrées et comment supprimer le service mobilité Site Recovery n’est plus nécessaire après la migration.



## <a name="verify-migration"></a>Vérifier la migration

Avant de configurer la récupération d’urgence, assurez-vous que la migration s’est effectuée correctement. Pour réussir une migration, après le basculement, sélectionnez l’option **Terminer la migration** pour chaque machine à migrer. 

## <a name="verify-the-azure-vm-agent"></a>Vérification de l’agent de machine virtuelle Azure

Chaque machine virtuelle Azure doit avoir le [agent de machine virtuelle Azure](../virtual-machines/extensions/agent-windows.md) installé. Pour répliquer des machines virtuelles Azure, Site Recovery installe une extension sur l’agent.

- Si l’ordinateur exécute la version 9.7.0.0 ou ultérieure du service mobilité Site Recovery, l’agent de machine virtuelle Azure est installé automatiquement par le service mobilité sur des machines virtuelles Windows. Dans les versions antérieures du service mobilité, vous devez installer l’agent automatiquement.
- Pour les machines virtuelles Linux, vous devez installer manuellement l’agent de machine virtuelle Azure. Vous devez uniquement installer l’agent de machine virtuelle Azure si le service mobilité est installé sur la machine migrée est la version 9.6 ou une version antérieure.


### <a name="install-the-agent-on-windows-vms"></a>Installer l’agent sur des machines virtuelles Windows

Si vous exécutez une version du service mobilité Site Recovery antérieures à 9.7.0.0 ou certaines autres besoin d’installer l’agent manuellement, procédez comme suit :  

1. Vous devez disposer d’autorisations d’administrateur sur la machine virtuelle.
2. Téléchargez le [programme d’installation de l’Agent de machine virtuelle](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Exécutez le fichier de programme d’installation.

#### <a name="validate-the-installation"></a>validation de l'installation
Pour vérifier que l’agent est installé :

1. Sur la machine virtuelle Azure, dans le dossier C:\WindowsAzure\Packages, vous devez voir le fichier WaAppAgent.exe.
2. Cliquez avec le bouton droit sur ce fichier et, dans **Propriétés**, sélectionnez l’onglet **Détails**.
3. Vérifiez que le champ **Version du produit** indique 2.6.1198.718 ou une version ultérieure.

[En savoir plus](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sur l’installation de l’agent pour Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installez l’agent sur les machines virtuelles Linux

Installer le [machine virtuelle Linux Azure](../virtual-machines/extensions/agent-linux.md) agent manuellement comme suit :

1. Vérifiez que vous disposez des autorisations d’administrateur sur l’ordinateur.
2. Nous recommandons vivement que vous installez l’agent Linux VM à l’aide d’un package RPM ou un package DEB à partir du référentiel de package de votre distribution. Tous les [fournisseurs de distribution approuvés](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) intègrent l’agent Azure Linux dans leurs images et référentiels.
    - Nous recommandons vivement que vous mettez à jour l’agent uniquement par le biais d’un référentiel de distribution.
    - Nous ne recommandons pas l’installation de l’agent Linux VM directement à partir de GitHub et sa mise à jour.
    -  Si la dernière version de l’agent n’est pas disponible pour la distribution, contactez le support de distribution pour savoir comment l’installer. 

#### <a name="validate-the-installation"></a>validation de l'installation 

1. Exécutez la commande suivante : **ps -e** pour vous assurer que l’agent Azure s’exécute sur la VM Linux.
2. Si le processus ne s’exécute pas, redémarrez-le à l’aide des commandes suivantes :
    - Pour Ubuntu : **walinuxagent démarrage du service**
    - Pour les autres distributions : **service waagent start**


## <a name="uninstall-the-mobility-service"></a>Désinstaller le service Mobilité

1. Désinstaller manuellement le service mobilité à partir de la machine virtuelle Azure, en utilisant l’une des méthodes suivantes. 
    - Pour Windows, dans le Panneau de configuration > **Ajout/Suppression de programmes**, désinstallez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery**. À partir d’une invite de commandes avec élévation de privilèges, exécutez :
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pour Linux, connectez-vous en tant qu’utilisateur racine. Sur un terminal, accédez à **/user/local/ASR** et exécutez la commande suivante :
        ```
        ./uninstall.sh -Y
        ```
2. Redémarrez la machine virtuelle avant de configurer la réplication.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes de révision](site-recovery-extension-troubleshoot.md) pour l’extension Site Recovery sur l’agent de machine virtuelle Azure.
[Répliquer rapidement](azure-to-azure-quickstart.md) une machine virtuelle Azure dans une région secondaire.
