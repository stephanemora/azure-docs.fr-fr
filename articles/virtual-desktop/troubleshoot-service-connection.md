---
title: Détecter un problème de connexion au service dans Windows Virtual Desktop – Azure
description: Comment résoudre des problèmes lorsque vous configurez des connexions à un client dans un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bf1af6ab0d8187452ecc2e48dcf72de1093690fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474099"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connexions au service Windows Virtual Desktop

Appuyez-vous sur cet article pour résoudre les problèmes liés aux connexions à un client Windows Virtual Desktop.

## <a name="provide-feedback"></a>Fournir des commentaires

Vous pouvez nous fournir des commentaires et discuter du service Windows Virtual Desktop avec l’équipe de produit et d’autres membres actifs de la communauté dans [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)

Si l’utilisateur peut démarrer les clients Bureau à distance et s’authentifier, il ne voit cependant aucune icône dans le flux de découverte web.

Vérifiez que l’utilisateur signalant les problèmes a été affecté aux groupes d’applications à l’aide de cette ligne de commande :

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Vérifiez que l’utilisateur se connecte avec les informations d’identification correctes.

Si le client web est utilisé, vérifiez qu’il n’y a aucun problème d’informations d’identification mises en cache.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Les machines virtuelles Windows 10 Entreprise multisession ne répondent pas

Si un ordinateur virtuel ne répond pas et que vous ne pouvez pas y accéder via le protocole RDP, vous devez résoudre le problème à l’aide de la fonctionnalité de diagnostic en vérifiant l’état de l’ordinateur hôte.

Pour vérifier l’état de l’ordinateur hôte, exécutez la cmdlet suivante :

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Si l’état de l’ordinateur hôte est `NoHeartBeat`, cela signifie que la machine virtuelle ne répond pas et que l’agent ne peut pas communiquer avec le service Windows Virtual Desktop.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

Vous pouvez effectuer quelques opérations pour corriger l’état NoHeartBeat.

### <a name="update-fslogix"></a>Mettre à jour FSLogix

Si votre application FSLogix n’est pas à jour, en particulier s’il s’agit de la version 2.9.7205.27375 de frxdrvvt.sys, cela peut provoquer un blocage. Veillez à [mettre à jour FSLogix vers la dernière version](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Désactiver BgTaskRegistrationMaintenanceTask

Si la mise à jour de FSLogix ne fonctionne pas, le problème est peut-être dû à un composant BiSrv qui épuise les ressources système pendant une tâche de maintenance hebdomadaire. Désactivez temporairement la tâche de maintenance en désactivant BgTaskRegistrationMaintenanceTask à l’aide d’une de ces deux méthodes :

- Accédez au menu Démarrer et recherchez **Planificateur de tâches**. Accédez à **Bibliothèque du planificateur de tâches** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Recherchez une tâche nommée **BgTaskRegistrationMaintenanceTask**. Une fois trouvée, cliquez dessus avec le bouton droit et sélectionnez **Désactiver** dans le menu déroulant.
- Ouvrez un menu de ligne de commandes en tant qu’administrateur et exécutez la commande suivante :
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Windows Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md).
