---
title: Connexions à un client Bureau à distance dans Windows Virtual Desktop - Azure
description: Comment résoudre des problèmes lorsque vous configurez des connexions à un client dans un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: c6c7a57a2093445d3922f9349242c9a902df7370
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300721"
---
# <a name="remote-desktop-client-connections"></a>Connexions au client Bureau à distance

Appuyez-vous sur cet article pour résoudre les problèmes liés aux connexions à un client Windows Virtual Desktop.

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="you-cant-open-a-web-client"></a>Vous ne pouvez pas ouvrir un client web

Vérifiez la connectivité Internet en ouvrant un autre site web, par exemple, [www.Bing.com](https://www.bing.com).

Utilisez **nslookup** pour vérifier que DNS peut résoudre le nom de domaine complet :

```cmd
nslookup rdweb.wvd.microsoft.com
```

Essayez de vous connecter avec un autre client, comme le client Bureau à distance pour Windows 7 ou Windows 10, puis vérifiez si vous pouvez ouvrir le client web.

### <a name="error-opening-another-site-fails"></a>Error: échec de l’ouverture d’un autre site

**Cause :** problèmes ou pannes de réseau.

**Correctif :** contactez le support réseau.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error: Nslookup ne peut pas résoudre le nom

**Cause :** problèmes ou pannes de réseau.

**Correctif :** contactez le support réseau.

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Error: vous ne pouvez pas vous connecter contrairement à d’autres clients

**Cause :** le comportement du navigateur n’était pas conforme à ce qui est attendu, et il a cessé de fonctionner.

**Correctif :** suivez ces instructions pour résoudre les problèmes du navigateur.

1. Redémarrez le navigateur.
2. Effacez les cookies du navigateur. Consultez l’article [How to delete cookie files in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer) (Suppression des fichiers de cookie dans Internet Explorer).
3. Videz le cache du navigateur. Consultez l’article [Clear browser cache for your browser](https://binged.it/2RKyfdU) (Vider le cache du navigateur).
4. Ouvrez le navigateur en mode privé.

## <a name="web-client-stops-responding-or-disconnects"></a>Le client web cesse de répondre ou se déconnecte

Essayez de vous connecter à l’aide d’un autre navigateur ou client.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Error: d’autres navigateurs et clients connaissent également un dysfonctionnement ou ne parviennent pas à s’ouvrir

**Cause :** problèmes ou pannes du réseau ou du système d’exploitation

**Correctif :** contactez les équipes du support technique.

## <a name="web-client-keeps-prompting-for-credentials"></a>Le client web ne cesse de demander les informations d’identification

Si le client web ne cesse de demander les informations d’identification, suivez ces instructions.

1. Vérifiez que l’URL du client web est correcte.
2. Vérifiez que les informations d’identification correspondent à l’environnement Windows Virtual Desktop lié à l’URL.
3. Effacez les cookies du navigateur. Consultez l’article [How to delete cookie files in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer) (Suppression des fichiers de cookie dans Internet Explorer).
4. Videz le cache du navigateur. Consultez l’article [Clear browser cache for your browser](https://binged.it/2RKyfdU) (Vider le cache du navigateur).
5. Ouvrez le navigateur en mode privé.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Le client Bureau à distance de Windows 7 ou Windows 10 ne répond plus ou ne peut pas être ouvert

Nettoyez les registres de client hors bande (OOB) à l’aide des cmdlets PowerShell suivantes.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Accédez à **%AppData%\RdClientRadc** et supprimez-en tout le contenu.

Désinstallez, puis réinstallez le client Bureau à distance pour Windows 7 et Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Résolution des problèmes de connectivité des utilisateurs finals

Parfois, les utilisateurs peuvent accéder à leurs ressources de flux et locales, mais ils rencontrent des problèmes de configuration, disponibilité ou performance qui les empêchent d’accéder aux ressources distantes. Le cas échéant, l’utilisateur reçoit des messages semblables aux suivants :

![Message d’erreur de Connexion Bureau à distance](media/eb76b666808bddb611448dfb621152ce.png)

![Message d’erreur dû à l’impossibilité de se connecter à la passerelle.](media/a8fbb9910d4672147335550affe58481.png)

Suivez ces instructions de dépannage générales pour les codes d’erreur de connexion de client.

1. Vérifiez le nom d’utilisateur et l’heure à laquelle le problème a été rencontré.
2. Ouvrez **PowerShell** et établissez une connexion avec le locataire Windows Virtual Desktop là où le problème a été signalé.
3. Vérifiez que la connexion est bien établie avec le locataire approprié à l’aide de la cmdlet **Get-RdsTenant**.
4. À l’aide des cmdlets **Get-RdsHostPool** et **Get-RdsSessionHost**, vérifiez que le problème a été résolu dans le pool d’hôtes correct.
5. Exécutez la commande ci-dessous pour obtenir la liste de toutes les activités en échec de type connexion pour la fenêtre de temps spécifiée :

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. À l’aide du paramètre **ActivityId** de la sortie de la cmdlet précédente, exécutez la commande ci-dessous :

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. La commande génère une sortie semblable à celle qui suit : Utilisez **ErrorCodeSymbolic** et **ErrorMessage** pour résoudre les problèmes de la cause racine.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Error: O_ADD_USER_TO_GROUP_FAILED/échec de l’ajout de l’utilisateur = ≤nom_utilisateur≥ au groupe = Utilisateurs du Bureau à distance. Raison : Win32.ERROR_NO_SUCH_MEMBER

**Cause :** aucune machine virtuelle n’a été jointe au domaine dans lequel figure l’objet utilisateur.

**Correctif :** ajoutez une machine virtuelle au domaine approprié. Consultez [Joindre une machine virtuelle Windows Server à un domaine managé](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error: Nslookup ne peut pas résoudre le nom

**Cause :** problèmes ou pannes de réseau.

**Correctif :** contactez le support réseau.

### <a name="error-connectionfailedclientprotocolerror"></a>Error: ConnectionFailedClientProtocolError

**Cause :** les machines virtuelles auxquelles l’utilisateur tente de se connecter ne sont pas jointes à un domaine.

**Correctif :** joignez toutes les machines virtuelles faisant partie d’un pool d’hôtes au contrôleur de domaine.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Error: ConnectionFailedUserSIDInformationMismatch
**Cause :** Le SID du jeton Azure Active Directory (AD) de l’utilisateur ne correspond pas au SID renvoyé par le contrôleur de domaine lors de la tentative d’activation de l’utilisateur pour la connexion à distance. Cette erreur se produit généralement lorsque vous tentez de vous connecter à un environnement Azure Active Directory Domain Services (Azure AD DS) avec un utilisateur initialement issu d’un Windows Server AD.

**Correctif :** Ce scénario n’est pas pris en charge pour l’instant. Seuls des utilisateurs provenant d’Azure Active Directory peuvent se connecter à des machines virtuelles Windows Virtual Desktop connectées à Azure AD DS.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)

Si l’utilisateur peut démarrer les clients Bureau à distance et s’authentifier, il ne voit cependant aucune icône dans le flux de découverte web.

Vérifiez que l’utilisateur signalant les problèmes a été affecté aux groupes d’applications à l’aide de cette ligne de commande :

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Vérifiez que l’utilisateur se connecte avec les informations d’identification correctes.

Si le client web est utilisé, vérifiez qu’il n’y a aucun problème d’informations d’identification mises en cache.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Windows Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour détecter les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md).
- Pour détecter les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez l’article [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour plus d’informations sur le service en préversion, consultez [Environnement Windows Virtual Desktop en préversion](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
