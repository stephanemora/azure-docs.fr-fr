---
title: Client et l’hôte de la création du pool dans un bureau virtuel Windows - Azure
description: Comment résoudre les problèmes lorsque vous configurez un client et la session hôte machine virtuelle (VM) dans un environnement de bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928121"
---
# <a name="tenant-and-host-pool-creation"></a>Création du pool de client et de l’hôte

Utilisez cet article pour résoudre les problèmes que vous rencontrez lors de la configuration du bureau virtuel Windows session héberger des ordinateurs virtuels (VM).

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="vms-are-not-joined-to-the-domain"></a>Machines virtuelles ne sont pas joints au domaine

Suivez ces instructions si vous rencontrez des problèmes de jonction de machines virtuelles au domaine.

- Joindre la machine virtuelle manuellement à l’aide du processus dans [joindre une machine virtuelle Windows Server à un domaine géré](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) ou à l’aide de la [modèle de jointure de domaine](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Essayez de tester le nom de domaine à partir de la ligne de commande sur la machine virtuelle.
- Passez en revue la liste des messages d’erreur de domaine jointure dans [résolution des problèmes des Messages d’erreur de jointure domaine](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Error: Informations d’identification incorrectes

**Cause :** Il a été une faute de frappe lorsque les informations d’identification ont été entrées dans correctifs de l’interface de modèle Azure Resource Manager.

**Correctif :** Suivez ces instructions pour corriger les informations d’identification.

1. Ajouter manuellement les machines virtuelles à un domaine.
2. Redéployer une fois que les informations d’identification ont été confirmées. Consultez [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Joindre des machines virtuelles à un domaine à l’aide d’un modèle avec [joint une machine virtuelle Windows existante au domaine AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Error: Délai d’attente pour l’entrée utilisateur

**Cause :** Le compte utilisé pour effectuer la jonction de domaine peut avoir une authentification multifacteur (MFA).

**Correctif :** Suivez ces instructions pour terminer la jonction de domaine.

1. Supprimer temporairement l’authentification Multifacteur pour le compte.
2. Utilisez un compte de service.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Error: Le compte utilisé lors de la configuration n’a pas les autorisations pour terminer l’opération

**Cause :** Le compte utilisé n’a pas les autorisations pour joindre des machines virtuelles au domaine en raison des réglementations de conformité et d’utilisation.

**Correctif :** Suivez ces instructions.

1. Utiliser un compte qui est membre du groupe Administrateurs.
2. Accordez les autorisations nécessaires pour le compte utilisé.

### <a name="error-domain-name-doesnt-resolve"></a>Error: Nom de domaine ne résout pas.

**Cause 1 :** Machines virtuelles sont dans un groupe de ressources qui n’est associé avec le réseau virtuel (VNET) où se trouve le domaine.

**Correctif 1 :** Créer une homologation entre le réseau virtuel dans lequel les machines virtuelles ont été configurées et le réseau virtuel dans lequel le contrôleur de domaine (DC) est en cours d’exécution. Consultez [créer une homologation de réseaux virtuels - Resource Manager, des abonnements différents](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Cause 2 :** Lorsque vous utilisez AadService (AADS), les entrées DNS n’ont pas été définies.

**Correctif 2 :** Pour définir les services de domaine, consultez [activer de Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agent de bureau virtuel Windows et Windows Virtual Desktop du chargeur de démarrage ne sont pas installés.

La méthode recommandée pour configurer des machines virtuelles à l’aide de Azure Resource Manager **créer et configurer un bureau virtuel Windows hébergent pool** modèle. Le modèle installe automatiquement l’Agent de bureau virtuel de Windows et le chargeur de démarrage de l’Agent Windows Virtual Desktop.

Suivez ces instructions pour confirmer que les composants sont installés et recherchez les messages d’erreur.

1. Vérifiez que les deux composants sont installés en vérifiant **le panneau de configuration** > **programmes** > **programmes et fonctionnalités**. Si **Agent de bureau virtuel Windows** et **chargeur de démarrage de l’Agent Windows Virtual Desktop** ne sont pas visibles, ils ne sont pas installés sur la machine virtuelle.
2. Ouvrez **Explorateur de fichiers** et accédez à **C:\Windows\Temp\scriptlogs.log**. Si le fichier est manquant, il indique que la DSC PowerShell qui a installé les deux composants n’a pas pu s’exécuter dans le contexte de sécurité fourni.
3. Si le fichier **C:\Windows\Temp\scriptlogs.log** n’est présente, ouvrez-le et recherchez les messages d’erreur.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Error: Agent de bureau virtuel Windows et le chargeur de démarrage de l’Agent Windows Virtual Desktop sont manquantes. C:\Windows\Temp\scriptlogs.log est également manquant

**Cause 1 :** Informations d’identification fournies pendant la saisie pour le modèle Azure Resource Manager sont incorrectes ou les autorisations sont insuffisantes.

**Correctif 1 :** Ajouter manuellement les composants manquants pour les machines virtuelles à l’aide de [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Cause 2 :** PowerShell DSC a réussi à démarrer et exécuter, mais a échoué car elle ne peut pas se connecter à un bureau virtuel Windows et obtenir les informations nécessaires.

**Correctif 2 :** Vérifiez les éléments dans la liste suivante.

- Assurez-vous que le compte ne dispose pas MFA.
- Vérifiez que le nom du client est correctes et que le locataire existe dans un bureau virtuel Windows.
- Confirmer le compte possède au moins les autorisations de contributeur de services Bureau à distance.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Error: Échec de l’authentification, l’erreur dans C:\Windows\Temp\scriptlogs.log

**Cause :** PowerShell DSC a été en mesure d’exécuter, mais n’a pas pu se connecter au bureau virtuel Windows.

**Correctif :** Vérifiez les éléments dans la liste suivante.

- Inscrire manuellement les machines virtuelles avec le service de bureau virtuel Windows.
- Vérifiez le compte utilisé pour la connexion au bureau virtuel Windows dispose des autorisations sur le client pour créer des pools de l’hôte.
- Confirmer le compte ne dispose pas MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent de bureau virtuel Windows n’est pas inscrit auprès du service Bureau virtuel Windows

Lorsque l’Agent de bureau virtuel Windows est tout d’abord installé sur session héberger les machines virtuelles (soit manuellement ou via le modèle Azure Resource Manager et PowerShell DSC), il fournit un jeton d’inscription. La section suivante couvre la résolution des problèmes applicables à l’Agent de bureau virtuel de Windows et le jeton.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Error: L’état archivé dans l’applet de commande Get-RdsSessionHost affiche un état non disponible

![Applet de commande Get-RdsSessionHost affiche un état non disponible.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Cause :** L’agent n’est pas en mesure de se mettre à jour vers une nouvelle version.

**Correctif :** Suivez ces instructions pour mettre à jour manuellement l’agent.

1. Téléchargez une nouvelle version de l’agent sur la machine virtuelle d’hôte de session.
2. Lancez le Gestionnaire des tâches et, dans l’onglet Service, arrêtez le service de RDAgentBootLoader.
3. Exécutez le programme d’installation pour la nouvelle version de l’Agent de bureau virtuel Windows.
4. Lorsque vous y êtes invité pour le jeton d’inscription, supprimez l’entrée INVALID_TOKEN et appuyez sur Suivant (un nouveau jeton n’est pas nécessaire).
5. Terminer l’Assistant installation.
6. Ouvrez le Gestionnaire des tâches et démarrer le service RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Error:  Entrée de Registre Windows Virtual Desktop Agent IsRegistered affiche la valeur 0

**Cause :** Jeton d’inscription a expiré ou a été généré avec la valeur d’expiration de 999999.

**Correctif :** Suivez ces instructions pour corriger des erreurs de Registre de l’agent.

1. S’il existe déjà un jeton d’inscription, supprimez-le avec Remove-RDSRegistrationInfo.
2. Générer un nouveau jeton avec les services Bureau à distance-NewRegistrationInfo.
3. Vérifiez que le paramètre - ExpriationHours est défini sur 72 (valeur maximale est 99 999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Error: Agent de bureau virtuel Windows n’est pas signalé une pulsation lors de l’exécution de Get-RdsSessionHost

**Cause 1 :** RDAgentBootLoader service a été arrêté.

**Correctif 1 :** Lancez le Gestionnaire des tâches et, si l’onglet Service signale un état arrêté pour RDAgentBootLoader service, démarrez le service.

**Cause 2 :** Le port 443 peut être fermé.

**Correctif 2 :** Suivez ces instructions pour ouvrir le port 443.

1. Vérifiez le port 443 est ouvert en téléchargeant l’outil PSPing à partir de [outils Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Installez PSPing sur l’hôte de session où l’agent est en cours d’exécution de machine virtuelle.
3. Ouvrez l’invite de commandes en tant qu’administrateur et exécutez la commande suivante :

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Vérifiez ces informations PSPing reçu à partir de la RDBroker :

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Résolution des problèmes avec la pile de côte à côte de bureau virtuel Windows

La pile de côte à côte de bureau virtuel Windows est automatiquement installée avec Windows Server 2019. Utilisez Microsoft Installer (MSI) pour installer la pile de côte à côte sur Microsoft Windows Server 2016 ou Windows Server 2012 R2. Pour Microsoft Windows 10, la pile de côte à côte de bureau virtuel Windows est activée avec **enablesxstackrs.ps1**.

Il existe trois méthodes principales de la pile côte à côte obtient installée ou activée sur les machines virtuelles du pool session hôte :

- Avec Azure Resource Manager **créer et approvisionner nouveau bureau virtuel Windows hôte pool** modèle
- En étant incluse et activée sur l’image principale
- Installé ou activé manuellement sur chaque machine virtuelle (ou avec les extensions/PowerShell)

Si vous rencontrez des problèmes avec la pile de côte à côte de bureau virtuel de Windows, tapez le **qwinsta** commande à partir de l’invite de commandes pour confirmer que la pile côte à côte est installée ou activée.

La sortie de **qwinsta** listera **rdp-sxs** dans la sortie si la pile côte à côte est installée et activée.

![Pile de côte-à-côte installé ou activé avec qwinsta répertorié comme rdp-côte à côte dans la sortie.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examinez les entrées de Registre répertoriées ci-dessous et vérifiez que leurs valeurs correspondent. Si les clés de Registre sont manquantes ou les valeurs sont incompatibles, suivez les instructions de [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) sur la réinstallation de la pile côte à côte.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Error: O_REVERSE_CONNECT_STACK_FAILURE

![Code d’erreur O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Cause :** La pile côte à côte n’est pas installée sur la machine virtuelle d’hôte de session.

**Correctif :** Suivez ces instructions pour installer la pile de côte à côte sur la machine virtuelle d’hôte de session.

1. Utilisez le protocole RDP (Remote Desktop) pour obtenir directement dans la machine virtuelle de l’hôte de session en tant qu’administrateur local.
2. Téléchargez et importez [Windows Virtual Desktop PowerShell module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell si vous n’avez pas déjà.
3. Installer la pile de côte à côte avec [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Comment la corriger une pile de côte à côte de bureau virtuel Windows présente des dysfonctionnements

Il existe des conditions qui peuvent provoquer la pile côte à côte un dysfonctionnement connus :

- Ne suivez ne pas le bon ordre des étapes pour activer la pile côte à côte
- Mise à jour automatique pour Windows 10 amélioré polyvalent disque (EVD)
- Manquant le rôle hôte de Session de bureau à distance (RDSH)
- En cours d’exécution enablesxsstackrc.ps1 plusieurs fois
- En cours d’exécution enablesxsstackrc.ps1 dans un compte qui n’a pas des privilèges d’administrateur local

Les instructions fournies dans cette section peuvent vous aider à désinstaller la pile de côte à côte de bureau virtuel Windows. Une fois que vous désinstallez la pile côte à côte, accédez à « Register de la machine virtuelle avec le pool de bureau hôte virtuel de Windows » [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) à réinstaller la pile côte à côte.

La machine virtuelle utilisée pour exécuter la mise à jour doit se trouver sur le même sous-réseau et le même domaine que la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement.

Suivez ces instructions pour exécuter la mise à jour à partir du même sous-réseau et domaine :

1. Se connecter avec standard protocole RDP (Remote Desktop) à la machine virtuelle à partir d’où le correctif est appliqué.
2. Télécharger PsExec à partir de https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Décompressez le fichier téléchargé.
4. Démarrez l’invite de commandes en tant qu’administrateur local.
5. Accédez au dossier où PsExec a été décompressé.
6. À partir de l’invite de commandes, utilisez la commande suivante :

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname est le nom de l’ordinateur de la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement.

7. Acceptez le contrat de licence de PsExec en cliquant sur Accepter.

    ![Capture d’écran du contrat de licence de logiciel.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Cette boîte de dialogue s’affiche uniquement lors de la première exécution PsExec.

8. Une fois que la session d’invite de commandes s’ouvre sur la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement, exécutez qwinsta et vérifier si une entrée nommée rdp-sxs est disponible. Si ce n’est pas le cas, une pile côte à côte n’est pas présente sur la machine virtuelle pour le problème n’est pas lié à la pile côte à côte.

    ![Invite de commandes administrateur](media/AdministratorCommandPrompt.png)

9. Exécutez la commande suivante, qui répertorie les composants de Microsoft installés sur la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement.

    ```cmd
        wmic product get name
    ```

10. Exécutez la commande ci-dessous avec les noms de produits à partir de l’étape précédente.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Désinstallez tous les produits qui commencent par « Bureau à distance ».

12. Une fois que tous les composants de bureau virtuel Windows ont été désinstallés, suivez les instructions pour votre système d’exploitation :

13. Si votre système d’exploitation est Windows Server, redémarrez la machine virtuelle ayant la pile côte à côte ne fonctionnant pas correctement (soit avec le portail Azure ou à partir de l’outil PsExec).

Si votre système d’exploitation est Microsoft Windows 10, suivez les instructions ci-dessous :

14. À partir de la machine virtuelle en cours d’exécution PsExec, ouvrez l’Explorateur de fichiers et copier disablesxsstackrc.ps1 sur le disque de système de la machine virtuelle avec la pile côte à côte malfunctioned.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname est le nom de l’ordinateur de la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement.

15. Le processus recommandé : à partir de l’outil PsExec, démarrez PowerShell et accédez au dossier à partir de l’étape précédente et exécutez disablesxsstackrc.ps1. Vous pouvez également exécuter les applets de commande suivantes :

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quand les applets de commande sont effectuées en cours d’exécution, redémarrez la machine virtuelle avec la pile côte à côte ne fonctionnant pas correctement.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble sur la résolution des problèmes de bureau virtuel Windows et les pistes d’escalade de verrous, consultez [résolution des problèmes de vue d’ensemble, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes lors de la création d’un pool de client et de l’hôte dans un environnement de bureau virtuel de Windows, consultez [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes lors de la configuration d’une machine virtuelle (VM) dans un bureau virtuel Windows, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion de client de bureau virtuel de Windows, consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md).
- Pour résoudre les problèmes lors de l’utilisation de PowerShell avec le bureau virtuel de Windows, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Suivez le [Didacticiel : Résoudre les problèmes de déploiements de modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).