---
title: Résoudre les problèmes d’échec de basculement vers Azure | Microsoft Docs
description: Cet article explique comment résoudre les erreurs courantes qui surviennent lors d’un basculement vers Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 2156ee6cf27ecfa32b19ad5bbef7549e99c3f7ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61280624"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Résoudre les erreurs se produisant lors du basculement d’une machine virtuelle VMware ou d'une machine physique vers Azure

Vous pouvez recevoir les erreurs suivantes lorsque vous procédez au basculement d’une machine virtuelle vers Azure. Pour résoudre les problèmes, servez-vous des étapes décrites pour chaque condition d’erreur.

## <a name="failover-failed-with-error-id-28031"></a>Le basculement a échoué avec l’ID d’erreur 28031

Site Recovery n’a pas pu créer de machine virtuelle basculée dans Azure. L’une des raisons suivantes peut en être la cause :

* Le quota est suffisant pour créer la machine virtuelle : Vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](https://aka.ms/getazuresupport) pour augmenter le quota.

* Vous tentez de basculer des machines virtuelles de familles de taille différentes dans le même groupe à haute disponibilité. Veillez à choisir le même ordre de grandeur de taille pour toutes les machines virtuelles d’un même groupe à haute disponibilité. Changez la taille en accédant aux paramètres Calcul et réseau de la machine virtuelle, puis réessayez le basculement.

* Il existe une stratégie sur l’abonnement qui empêche la création d’une machine virtuelle. Modifiez la stratégie pour permettre la création d’une machine virtuelle, puis réessayez le basculement.

## <a name="failover-failed-with-error-id-28092"></a>Le basculement a échoué avec l’ID d’erreur 28092

Site Recovery n’a pas pu créer d’interface réseau pour la machine virtuelle basculée. Assurez-vous que vous avez un quota suffisant pour créer des interfaces réseau dans l’abonnement. Vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](https://aka.ms/getazuresupport) pour augmenter le quota. Si vous disposez d’un quota suffisant, le problème peut être occasionnel, recommencez l’opération. Si le problème persiste même après plusieurs tentatives, laissez un commentaire à la fin de ce document.  

## <a name="failover-failed-with-error-id-70038"></a>Le basculement a échoué avec l’ID d’erreur 70038

Site Recovery n’a pas pu créer de machine virtuelle classique basculée dans Azure. Les raisons suivantes peuvent en être la cause :

* Une des ressources, comme un réseau virtuel, nécessaire à la création de la machine virtuelle n’existe pas. Créez le réseau virtuel, tel que proposé sous les paramètres Calcul et réseau de la machine virtuelle, ou modifiez le paramètre sur un réseau virtuel qui existe déjà et réessayez le basculement.

## <a name="failover-failed-with-error-id-170010"></a>Le basculement a échoué avec l’ID d’erreur 170010

Site Recovery n’a pas pu créer de machine virtuelle basculée dans Azure. Cela peut être causé par l’échec d’une activité interne d’alimentation pour la machine virtuelle locale.

Pour afficher une machine dans Azure, l’environnement Azure exige que certains pilotes soient à l’état Démarrage et que des services comme DHCP soient à l’état Démarrage automatique. Au moment du basculement, l’activité d’alimentation convertit donc le type de démarrage des **pilotes atapi, intelide, storflt, vmbus et storvsc** en Démarrage, et celui de certains services comme DHCP en Démarrage automatique. Cette activité peut échouer en raison de problèmes propres à l’environnement. 

Pour modifier manuellement le type de démarrage des pilotes pour **SE invité Windows**, suivez les étapes ci-dessous :

1. [Téléchargez](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) le script de non-alimentation, puis exécutez-le de la façon suivante. Ce script détermine si la machine virtuelle a besoin d’une alimentation.

    `.\Script-no-hydration.ps1`

    Il donne le résultat suivant si une alimentation est nécessaire :

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Dans le cas où la machine virtuelle remplit l’exigence de non-alimentation, le script donne le résultat « This system meets no-hydration requirement ». Tous les pilotes et services se trouvent à l’état requis par Azure ; l’alimentation n’est pas nécessaire sur la machine virtuelle.

2. Exécutez ainsi le script no-hydration-set si la machine virtuelle ne répond pas à l’exigence de non-alimentation.

    `.\Script-no-hydration.ps1 -set`
    
    Il convertit le type de démarrage des pilotes et donne le résultat ci-dessous :
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Impossible de se connecter ou d’établir une liaison RDP ou SSH à la machine virtuelle après son basculement, car le bouton Se connecter est grisé

Si le bouton **Se connecter** de la machine virtuelle basculée dans Azure est grisé et que vous n’avez pas établi de connexion ExpressRoute ou réseau privé virtuel de site à site à Azure :

1. Accédez à **Machine virtuelle** > **Réseaux**, cliquez sur le nom de l’interface réseau concernée.  ![interface-réseau](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Accédez à **Configurations d’adresses IP**, puis cliquez sur le champ Nom de la configuration d’adresse IP souhaitée. ![Configurations d’adresses IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Pour activer l’adresse IP publique, cliquez sur **Activer**. ![Activer l’adresse IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Cliquez sur **Configurer les paramètres requis** > **Créer**. ![Créer](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Entrez le nom de l’adresse publique, choisissez les options par défaut pour **SKU** et **Affectation**, puis cliquez sur **OK**.
6. Pour enregistrer les modifications, cliquez sur **Enregistrer**.
7. Fermez les panneaux et accédez à la section **Vue d’ensemble** de la machine virtuelle pour vous connecter/établir une liaison RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Impossible de se connecter/RDP/SSH – Le bouton Se connecter de la machine virtuelle est disponible

Si le bouton **Se connecter** de la machine virtuelle basculée dans Azure est disponible (s’il n’est pas grisé), cochez **Diagnostics de démarrage** sur votre machine virtuelle et recherchez les erreurs mentionnées dans [cet article](../virtual-machines/windows/boot-diagnostics.md).

1. Si la machine virtuelle n’a pas démarré, tentez de basculer vers un point de récupération plus ancien.
2. Si l’application de la machine virtuelle n’est pas disponible, tentez de basculer vers un point de récupération cohérent au niveau application.
3. Si la machine virtuelle est jointe au domaine, vérifiez que le contrôleur de domaine fonctionne bien. Pour cela, vous pouvez suivre les étapes ci-dessous :

    a. Créez une machine virtuelle dans le même réseau.

    b.  Veillez à ce qu’elle puisse être jointe au domaine sur lequel la machine virtuelle devrait apparaître après son basculement.

    c. Si le contrôleur de domaine ne fonctionne **pas** bien, essayez de vous connecter à la machine virtuelle basculée à l’aide d’un compte Administrateur local.
4. Si vous utilisez un serveur DNS personnalisé, vérifiez qu’il est accessible. Pour cela, vous pouvez suivre les étapes ci-dessous :

    a. Créez une machine virtuelle dans le même réseau et

    b. Vérifiez que la machine virtuelle parvient à effectuer la résolution de noms à l’aide du serveur DNS personnalisé

>[!Note]
>Tout paramètre autre que les diagnostics de démarrage nécessite l’installation de l’agent de machine virtuelle Azure sur la machine virtuelle avant le basculement.

## <a name="unexpected-shutdown-message-event-id-6008"></a>Message d’arrêt inattendu (ID d’événement 6008)

Lors du démarrage d’un machine virtuelle Windows après le basculement, si un message d’arrêt inattendu s’affiche sur la machine virtuelle récupérée, cela indique qu’un état d’arrêt de la machine virtuelle n’a pas été capturé dans le point de récupération utilisé pour le basculement. Cela se produit lorsque vous récupérez à un point où la machine virtuelle n'avait pas été complètement arrêtée.

Il ne s’agit normalement pas d’une source de préoccupation et cela peut généralement être ignoré pour les basculements non planifiés. Si le basculement est planifié, assurez-vous que la machine virtuelle est correctement s’est arrêté avant le basculement et laisser un délai suffisant pour en attente de réplication données en local à envoyer à Azure. Utilisez ensuite l’option **Plus récent** sur l’[écran de basculement](site-recovery-failover.md#run-a-failover) pour que toutes les données en attente sur Azure soient traitées en un point de récupération, qui est ensuite utilisé pour le basculement de la machine virtuelle.

## <a name="unable-to-select-the-datastore"></a>Impossible de sélectionner la banque de données

Ce problème est indiqué lorsque vous ne pouvez pas voir le portail de la banque de données dans Azure lorsque vous tentez de reprotéger la machine virtuelle qui a subi un basculement. Il s’agit, car le maître cible n’est pas reconnue comme une machine virtuelle sous vCenter ajoutés à Azure Site Recovery.

Pour plus d’informations sur la reprotéger une machine virtuelle, consultez [Reprotéger et effectuer automatiquement des machines vers un site local après le basculement vers Azure](vmware-azure-reprotect.md).

Pour résoudre le problème :

Créer manuellement le maître cible dans le serveur vCenter qui gère votre machine source. La banque de données seront disponible après les prochaine vCenter découverte et l’actualisation des opérations de structure.

> [!Note]
> 
> Les opérations de fabric de détection et d’actualisation peuvent prendre jusqu'à 30 minutes. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>L’inscription du serveur cible maître Linux avec CS échoue avec une erreur SSL 35 

L’inscription du serveur cible maître de récupération de Site Azure avec le serveur de configuration échoue en raison du Proxy authentifié en cours d’activation sur le serveur cible maître. 
 
Cette erreur est indiquée par les chaînes suivantes dans le journal d’installation : 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Pour résoudre le problème :
 
1. Sur la machine virtuelle du serveur de configuration, ouvrez une invite de commandes et vérifiez les paramètres de proxy à l’aide des commandes suivantes :

    CAT /etc/environment echo $http_proxy echo $https_proxy 

2. Si la sortie des commandes précédentes indique que le http_proxy ou https_proxy paramètres sont définis, utilisez une des méthodes suivantes pour débloquer les communications de serveur cible maître avec le serveur de configuration :
   
   - Téléchargez le [outil PsExec](https://aka.ms/PsExec).
   - Utilisez l’outil pour accéder au contexte d’utilisateur système et déterminer si l’adresse proxy est configuré. 
   - Si le proxy est configuré, ouvrez Internet Explorer dans un contexte utilisateur du système à l’aide de l’outil PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Pour vous assurer que le serveur cible maître peut communiquer avec le serveur de configuration :
  
     - Modifiez les paramètres de proxy dans Internet Explorer pour contourner l’adresse IP cible maître via le proxy.   
     Ou
     - Désactiver le proxy sur le serveur cible maître. 


## <a name="next-steps"></a>Étapes suivantes
- Résoudre les problèmes de [connexion à la machine virtuelle Windows via RDP](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Résoudre les problèmes de [connexion à la machine virtuelle Linux via SSH](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Si vous avez besoin d’aide supplémentaire, posez votre question sur le [Forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou laissez un commentaire à la fin de ce document. Notre communauté est active et doit être en mesure de vous aider.
