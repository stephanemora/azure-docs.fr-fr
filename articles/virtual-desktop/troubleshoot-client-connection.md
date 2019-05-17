---
title: Connexions client Bureau à distance dans un bureau virtuel Windows - Azure
description: Comment résoudre les problèmes lorsque vous configurez des connexions clientes dans un environnement de client de bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 99295fd4581cd81751f7d64b694c853efe51a106
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522938"
---
# <a name="remote-desktop-client-connections"></a>Connexions au client Bureau à distance

Utilisez cet article pour résoudre les problèmes avec les connexions de client de bureau virtuel Windows.

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="you-cant-open-a-web-client"></a>Vous ne pouvez pas ouvrir un client web

Confirmer il existe une connectivité internet en ouvrant un autre site web ; par exemple, [www.Bing.com](https://www.bing.com).

Utilisez **nslookup** pour confirmer DNS peut résoudre le nom de domaine complet :

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Essayez de vous connecter avec un autre client, comme le client Bureau à distance pour Windows 7 ou Windows 10 et vérifiez si vous pouvez ouvrir le client web.

### <a name="error-opening-another-site-fails"></a>Error: Ouverture d’un autre site échoue

**Cause :** Problèmes de réseau et/ou des pannes.

**Correctif :** Contactez le support de réseau.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error: Nslookup ne peut pas résoudre le nom

**Cause :** Problèmes de réseau et/ou des pannes.

**Correctif :** Contactez le support de réseau

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Error: Impossible de se connecter mais d’autres clients peuvent se connecter

**Cause :** Le navigateur ne se comporte en tant que travail attendu et arrêté.

**Correctif :** Suivez ces instructions pour résoudre les problèmes le navigateur.

1. Redémarrer le navigateur.
2. Cookies du navigateur clair. Consultez [comment supprimer des fichiers de cookies dans Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Cache de navigateur clair. Consultez [effacer le cache du navigateur pour votre navigateur](https://binged.it/2RKyfdU).
4. Ouvrir le navigateur en mode privé.

## <a name="web-client-stops-responding-or-disconnects"></a>Client Web cesse de répondre ou se déconnecte

Essayez de vous connecter à l’aide d’un autre navigateur ou client.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Error: Autres navigateurs et clients également un dysfonctionnement ou ne parviennent pas à ouvrir

**Cause :** Problèmes de réseau et/ou d’opération du système ou de pannes

**Correctif :** Contactez le support technique équipes.

## <a name="web-client-keeps-prompting-for-credentials"></a>Client Web conserve demande d’informations d’identification

Si le client Web conserve demander des informations d’identification, suivez ces instructions.

1. Vérifiez le QU'URL de client web est correcte.
2. Vérifiez que les informations d’identification sont pour l’environnement de bureau virtuel Windows liée à l’URL.
3. Cookies du navigateur clair. Consultez [comment supprimer des fichiers de cookies dans Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Cache de navigateur clair. Consultez [effacer le cache du navigateur pour votre navigateur](https://binged.it/2RKyfdU).
5. Ouvrir le navigateur en mode privé.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Client Bureau à distance pour Windows 7 ou Windows 10 ne répond plus ou ne peut pas être ouvert.

Utilisez les applets de commande PowerShell suivante pour nettoyer les registres de client hors bande (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Accédez à **%AppData%\RdClientRadc** et supprimer tout le contenu.

Désinstallez et réinstallez le client Bureau à distance pour Windows 7 et Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Résolution des problèmes de connectivité de l’utilisateur final

Parfois, les utilisateurs peuvent accéder à leurs flux et les ressources locales, mais rencontrez des problèmes de performances qui les empêchent d’accéder aux ressources distantes, la disponibilité ou la configuration. Dans ce cas, l’utilisateur bénéficie des messages similaires aux suivants :

![Message d’erreur de connexion Bureau à distance.](media/eb76b666808bddb611448dfb621152ce.png)

![Impossible de se connecter au message d’erreur de passerelle.](media/a8fbb9910d4672147335550affe58481.png)

Suivez ces instructions de dépannage générales pour les codes d’erreur de connexion de client.

1. Vérifiez le nom d’utilisateur et l’heure lorsque le problème a été rencontré.
2. Ouvrez **PowerShell** et établir la connexion au client de bureau virtuel Windows où le problème a été signalé.
3. Confirmer la connexion au locataire correct avec **Get-RdsTenant.**
4. À l’aide de **Get-RdsHostPool** et **Get-RdsSessionHost** applets de commande, vérifiez que dépannage est effectuée sur le pool d’hôte correct.
5. Exécutez la commande ci-dessous pour obtenir une liste de toutes les activités ayant échouées d’une connexion de type pour la fenêtre de temps spécifié :

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. À l’aide de la **ActivityId** à partir de la sortie précédente de l’applet de commande, exécutez la commande suivante :

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. La commande produit une sortie similaire à la sortie illustrée ci-dessous. Utilisez **ErrorCodeSymbolic** et **ErrorMessage** pour résoudre les problèmes de la cause racine.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Error: O_ADD_USER_TO_GROUP_FAILED / Impossible d’ajouter l’utilisateur = ≤username≥ au groupe = utilisateurs du Bureau à distance. Raison : Win32.ERROR_NO_SUCH_MEMBER

**Cause :** Machine virtuelle n’a pas été joint au domaine où s’objet utilisateur.

**Correctif :** Ajouter la machine virtuelle au domaine approprié. Consultez [joindre une machine virtuelle Windows Server à un domaine géré](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error: Nslookup ne peut pas résoudre le nom

**Cause :** Problèmes de réseau ou de pannes.

**Correctif :** Contactez le support de réseau

### <a name="error-connectionfailedclientprotocolerror"></a>Error: ConnectionFailedClientProtocolError

**Cause :** Machines virtuelles que l’utilisateur tente de se connecter à ne sont pas joints au domaine.

**Correctif :** Joindre toutes les machines virtuelles qui font partie d’un pool de l’hôte au contrôleur de domaine.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Utilisateur se connecte, mais rien ne s’affiche (aucun flux)

Un utilisateur peut démarrer les clients Bureau à distance et est en mesure d’authentifier, cependant l’utilisateur ne voit pas toutes les icônes dans la flux de découverte web.

Vérifiez que l’utilisateur signale les problèmes déjà associé aux groupes d’application à l’aide de cette ligne de commande :

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Vérifiez que l’utilisateur est connecté avec les informations d’identification correctes.

Si le client web est utilisé, vérifiez qu’il n’y a aucun problème d’informations d’identification mises en cache.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble sur la résolution des problèmes de bureau virtuel Windows et les pistes d’escalade de verrous, consultez [résolution des problèmes de vue d’ensemble, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes lors de la création d’un pool de client et de l’hôte dans un environnement de bureau virtuel de Windows, consultez [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes lors de la configuration d’une machine virtuelle (VM) dans un bureau virtuel Windows, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes lors de l’utilisation de PowerShell avec le bureau virtuel de Windows, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Suivez le [Didacticiel : Résoudre les problèmes de déploiements de modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).