---
title: Joindre une machine virtuelle Windows Server à un domaine managé | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment joindre une machine virtuelle Windows Server à un domaine managé Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: iainfou
ms.openlocfilehash: 3fd2a50946f0857d527c34b62687b2dbdd71298e
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172040"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Didacticiel : Joindre une machine virtuelle Windows Server à un domaine géré

Azure Active Directory Domain Services (Azure AD DS) fournit des services de domaine managés, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP, et l’authentification Kerberos/NTLM entièrement compatible avec Windows Server Active Directory. Avec un domaine managé Azure AD DS, vous pouvez fournir des fonctionnalités de jonction de domaine et de gestion à des machines virtuelles dans Azure. Ce tutoriel vous montre comment créer une machine virtuelle Windows Server, puis la joindre à un domaine managé Azure AD DS.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows Server
> * Se connecter à la machine virtuelle Windows Server sur un réseau virtuel Azure
> * Joindre la machine virtuelle au domaine managé Azure AD DS

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin des ressources suivantes :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, [créez et configurez une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.
    * Vérifiez que la synchronisation du hachage de mot de passe ou que la réinitialisation du mot de passe en libre-service d’Azure AD Connect a été effectuée pour que le compte puisse se connecter au domaine managé Azure AD DS.

Si vous disposez déjà d’une machine virtuelle que vous voulez joindre à un domaine, passez à la section expliquant comment [joindre la machine virtuelle au domaine managé Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans ce tutoriel, vous créez une machine virtuelle Windows Server à joindre à votre domaine managé Azure AD DS avec le portail Azure. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Créer une machine virtuelle Windows Server

Pour voir comment joindre un ordinateur à un domaine managé Azure AD DS, créons une machine virtuelle Windows Server. Cette machine virtuelle est connectée à un réseau virtuel Azure qui fournit la connectivité au domaine managé Azure AD DS. Le processus de jonction à un domaine managé Azure AD DS est identique à celui d’un domaine Active Directory Domain Services local normal.

Si vous disposez déjà d’une machine virtuelle que vous voulez joindre à un domaine, passez à la section expliquant comment [joindre la machine virtuelle au domaine managé Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. Dans le coin supérieur gauche du portail Azure, sélectionnez **+ Créer une ressource**.
2. Dans **Bien démarrer**, choisissez **Windows Server 2016 Datacenter**.

    ![Choisir de créer une machine virtuelle Windows Server 2016 Datacenter dans le portail Azure](./media/join-windows-vm/select-vm-image.png)

3. Dans la fenêtre **De base**, configurez les paramètres principaux de la machine virtuelle. Conservez les paramètres par défaut pour *Options de disponibilité*, *Image* et *Taille*.

    | Paramètre            | Valeur suggérée   |
    |----------------------|-------------------|
    | Resource group       | Sélectionnez ou créez un groupe de ressources, par exemple *myResourceGroup* |
    | Nom de la machine virtuelle | Entrez un nom pour la machine virtuelle, par exemple *myVM* |
    | Région               | Choisissez la région où créer votre machine virtuelle, par exemple *USA Est*. |
    | Nom d’utilisateur             | Entrez un nom d’utilisateur pour le compte d’administrateur local à créer sur la machine virtuelle, par exemple *azureuser* |
    | Mot de passe             | Entrez puis confirmez un mot de passe sécurisé pour l’administrateur local à créer sur la machine virtuelle. Ne spécifiez pas les informations d’identification d’un compte d’utilisateur du domaine. |

4. Par défaut, les machines virtuelles créées dans Azure ne sont pas accessibles depuis Internet. Cette configuration permet d’améliorer la sécurité de la machine virtuelle et de réduire le champ des attaques potentielles. Dans l’étape suivante de ce tutoriel, vous devez vous connecter à la machine virtuelle avec le protocole RDP (Remote Desktop Protocol), puis joindre le serveur Windows Server au domaine managé Azure AD DS.

    Quand RDP est activé, des attaques de connexion automatique sont susceptibles de se produire, ce qui peut désactiver les comptes avec des noms communs, comme *admin* ou *administrator* en raison de plusieurs tentatives de connexion successives en échec. RDP doit être activé seulement quand c’est nécessaire et être limité à un ensemble de plages d’adresses IP autorisées. L’[accès juste-à-temps Azure aux machines virtuelles][jit-access] dans le cadre d’Azure Security Center peut permettre ces sessions RDP de courte durée et limitées. Vous pouvez également [créer et utiliser un hôte Azure Bastion (actuellement en préversion)][azure-bastion] pour autoriser l’accès uniquement via le portail Azure sur SSL.

    Pour ce tutoriel, activez manuellement les connexions RDP à la machine virtuelle.

    Sous **Ports d’entrée publics**, sélectionnez l’option pour **Autoriser les ports sélectionnés**. Dans le menu déroulant pour **Sélectionner les ports entrants**, choisissez *RDP*.

5. Quand vous avez terminé, sélectionnez **Suivant : Disques**.
6. Dans le menu déroulant pour le **Type de disque de système d’exploitation**, choisissez *SSD Standard*, puis sélectionnez **Suivant : Mise en réseau**.
7. Votre machine virtuelle doit se connecter à un sous-réseau du réseau virtuel Azure qui peut communiquer avec le sous-réseau où votre domaine managé Azure AD DS est déployé. Nous recommandons de déployer un domaine managé Azure AD DS dans son propre sous-réseau dédié. Ne déployez pas votre machine virtuelle dans le même sous-réseau que votre domaine managé Azure AD DS.

    Il existe deux manières principales de déployer votre machine virtuelle et de se connecter à un sous-réseau de réseau virtuel approprié :
    
    * Créez ou sélectionnez un sous-réseau existant dans le même réseau virtuel que celui où votre domaine managé Azure AD DS est déployé.
    * Sélectionnez un sous-réseau dans un réseau virtuel Azure qui lui est connecté avec le [peering de réseau virtuel Azure][vnet-peering].
    
    Si vous sélectionnez un sous-réseau de réseau virtuel qui n’est pas connecté au sous-réseau pour votre instance Azure AD DS, vous ne pouvez pas joindre la machine virtuelle au domaine managé. Pour ce tutoriel, créons un sous-réseau dans le réseau virtuel Azure.

    Dans le volet **Mise en réseau**, sélectionnez le réseau virtuel où le domaine managé Azure AD DS est déployé, par exemple *myVnet*.
8. Dans cet exemple, le sous-réseau *DomainServices* existant est celui auquel le domaine managé Azure AD DS domaine est connecté. Ne connectez pas votre machine virtuelle à ce sous-réseau. Pour créer un sous-réseau pour la machine virtuelle, sélectionnez **Gérer la configuration du sous-réseau**.

    ![Choisir de gérer la configuration du sous-réseau dans le portail Azure](./media/join-windows-vm/manage-subnet.png)

9. Sélectionnez **+ Sous-réseau**, puis entrez un nom pour le sous-réseau, par exemple *ManagedVMs*. Spécifiez une **Plage d’adresses (bloc CIDR)** , par exemple *10.1.1.0/24*. Vérifiez que cette plage d’adresses IP ne chevauche pas d’autres plages d’adresses Azure ou locales existantes. Laissez les autres options avec leur valeur par défaut, puis sélectionnez **OK**.

    ![Créer une configuration de sous-réseau dans le portail Azure](./media/join-windows-vm/create-subnet.png)

10. La création du sous-réseau prend quelques secondes. Une fois qu’il est créé, sélectionnez le *X* pour fermer la fenêtre du sous-réseau.
11. De retour dans le volet **Mise en réseau** pour créer une machine virtuelle, choisissez le sous-réseau que vous avez créé dans le menu déroulant, par exemple *ManagedVMs*. Là encore, veillez à choisir le sous-réseau approprié et ne déployez pas votre machine virtuelle dans le même sous-réseau que votre domaine managé Azure AD DS.
12. Laissez les autres options avec leur valeur par défaut, puis sélectionnez **Gestion**.
13. Définissez **Diagnostics de démarrage** sur *Désactivé*. Laissez les autres options avec leur valeur par défaut, puis sélectionnez **Vérifier + créer**.
14. Passez en revue vos paramètres de la machine virtuelle, puis sélectionnez **Créer**.

La création de la machine virtuelle ne nécessite que quelques minutes. Le portail Azure montre l’état du déploiement. Quand votre machine virtuelle est prête, sélectionnez **Accéder à la ressource**.

![Accéder à la ressource de machine virtuelle dans le portail Azure une fois qu’elle a été créée](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Se connecter à la machine virtuelle Windows Server

Connectons-nous maintenant à la machine virtuelle Windows Server nouvellement créée avec RDP et joignons-la au domaine managé Azure AD DS. Utilisez les informations d’identification de l’administrateur local que vous avez spécifiées lors de la création de la machine virtuelle à l’étape précédente, et non pas des informations d’identification du domaine existantes.

1. Dans le volet **Vue d’ensemble** du laboratoire, sélectionnez **Connexion**.

    ![Se connecter à une machine virtuelle Windows dans le portail Azure](./media/join-windows-vm/connect-to-vm.png)

1. Sélectionnez l’option *Télécharger le fichier RDP*. Enregistrez ce fichier RDP dans votre navigateur web.
1. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
1. Entrez les informations d’identification de l’administrateur local que vous avez entrées à l’étape précédente pour créer la machine virtuelle, par exemple *localhost\azureuser*
1. Si vous voyez un avertissement lié au certificat pendant le processus de connexion, sélectionnez **Oui** ou **Continuer** pour vous connecter.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Joindre la machine virtuelle au domaine managé Azure AD DS

Une fois la machine virtuelle créée et une connexion RDP établie, joignons maintenant la machine virtuelle Windows Server au domaine managé Azure AD DS. Ce processus est identique à celui d’un ordinateur qui se connecte à un domaine Active Directory Domain Services local normal.

1. Le **Gestionnaire de serveur** doit s’ouvrir par défaut quand vous vous connectez à la machine virtuelle. Si ce n’est pas le cas, dans le menu **Démarrer**, sélectionnez **Gestionnaire de serveur**.
1. Dans le volet gauche de la fenêtre **Gestionnaire de serveur**, cliquez sur l’option **Serveur local**. Sous **Propriétés** dans le volet droit, choisissez **Groupe de travail**.

    ![Ouvrez Gestionnaire de serveur sur la machine virtuelle et modifiez la propriété du groupe de travail.](./media/join-windows-vm/server-manager.png)

1. Dans la fenêtre **Propriétés système**, sélectionnez **Modifier** pour vous joindre au domaine managé Azure AD DS.

    ![Choisir de modifier les propriétés de groupe de travail ou du domaine](./media/join-windows-vm/change-domain.png)

1. Dans la zone**Domaine**, spécifiez le nom de votre domaine managé Azure AD DS, par exemple *contoso.com*, puis sélectionnez **OK**.

    ![Spécifier le domaine managé Azure AD DS auquel se joindre](./media/join-windows-vm/join-domain.png)

1. Entrez les informations d’identification du domaine pour vous joindre au domaine. Utilisez les informations d’identification d’un utilisateur appartenant au groupe *Administrateurs Azure AD DC*. Seuls les membres de ce groupe ont les privilèges nécessaires pour pouvoir joindre des machines au domaine managé Azure AD DS. Les informations d’identification du compte peuvent être spécifiées de l’une des manières suivantes :

    * **Format UPN** (recommandé) : Entrez le suffixe du nom de l’utilisateur principal (UPN) pour le compte d’utilisateur, tel qu’il est configuré dans Azure AD. Par exemple, le suffixe UPN de l’utilisateur *contosoadmin* serait `contosoadmin@contoso.onmicrosoft.com`. Il existe deux cas d’utilisation courants dans lesquels le format UPN peut être utilisé de façon fiable pour se connecter au domaine, au lieu du format *SAMAccountName* :
        * Si le préfixe UPN d’un utilisateur est long, par exemple *deehasareallylongname*, le *SAMAccountName* peut être généré automatiquement.
        * Si plusieurs utilisateurs ont le même préfixe UPN au sein de votre locataire Azure AD, par exemple, *dee*, leur format *SAMAccountName* peut être généré automatiquement.
    * **Format SAMAccountName** : Entrez le nom du compte au format *SAMAccountName*. Par exemple, le *SAMAccountName* de l’utilisateur *contosoadmin* serait `CONTOSO\contosoadmin`.

1. Quelques secondes sont nécessaires pour se joindre au domaine managé Azure AD DS. Quand c’est terminé, le message suivant vous accueille dans le domaine :

    ![Bienvenue dans le domaine](./media/join-windows-vm/join-domain-successful.png)

    Sélectionnez **OK** pour continuer.

1. Pour terminer le processus de jonction au domaine managé Azure AD DS, redémarrez la machine virtuelle.

> [!TIP]
> Vous pouvez joindre une machine virtuelle à un domaine en utilisant PowerShell avec l’applet de commande [Add-Computer][add-computer]. L’exemple suivant effectue une jonction au domaine *CONTOSO*, puis redémarre la machine virtuelle. Quand vous y êtes invité, entrez les informations d’identification d’un utilisateur appartenant au groupe *Administrateurs Azure AD DC* :
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Pour joindre une machine virtuelle à un domaine sans vous y connecter et configurer manuellement la connexion, vous pouvez également explorer l’utilisation de l’applet de commande Azure PowerShell [Set-AzVmAdDomainExtension][set-azvmaddomainextension].

Une fois la machine virtuelle Windows Server redémarrée, toutes les stratégies appliquées dans le domaine managé Azure AD DS sont envoyées (push) à la machine virtuelle. Vous pouvez également vous connecter à la machine virtuelle Windows Server en utilisant des informations d’identification du domaine appropriées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans le tutoriel suivant, vous utilisez cette machine virtuelle Windows Server pour installer les outils de gestion qui vous permettent d’administrer le domaine managé Azure AD DS. Si vous ne voulez pas continuer dans cette série de tutoriels, passez en revue les étapes de nettoyage suivantes pour [désactiver RDP](#disable-rdp) ou pour [supprimer la machine virtuelle](#delete-the-vm). Sinon, [passez au tutoriel suivant](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Annuler la jonction de la machine virtuelle au domaine managé Azure AD DS

Pour supprimer la machine virtuelle du domaine managé Azure AD DS, suivez à nouveau les étapes permettant de [joindre la machine virtuelle à un domaine](#join-the-vm-to-the-azure-ad-ds-managed-domain). Au lieu de joindre le domaine managé Azure AD DS, choisissez de joindre un groupe de travail, comme le *WORKGROUP* (groupe de travail) par défaut. Une fois la machine virtuelle redémarrée, l’objet ordinateur est supprimé du domaine managé Azure AD DS.

Si vous [supprimez la machine virtuelle](#delete-the-vm) sans annuler la jonction au domaine, un objet ordinateur orphelin est laissé dans Azure AD DS.

### <a name="disable-rdp"></a>Désactiver RDP

Si vous continuez à utiliser la machine virtuelle Windows Server créée dans ce tutoriel pour exécuter vos propres applications ou charges de travail, rappelez-vous que RDP a été ouvert sur Internet. Pour améliorer la sécurité et réduire le risque d’attaque, RDP doit être désactivé sur Internet. Pour désactiver RDP sur la machine virtuelle Windows Server sur Internet, effectuez les étapes suivantes :

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Choisissez votre groupe de ressources, par exemple *myResourceGroup*.
1. Choisissez votre machine virtuelle, par exemple *myVM*, puis sélectionnez *Mise en réseau*.
1. Sous **Règles de sécurité réseau de trafic entrant** pour le groupe de sécurité réseau, sélectionnez la règle qui autorise RDP, puis choisissez **Supprimer**. La suppression de la règle de sécurité de trafic entrant prend quelques secondes.

### <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Si vous ne voulez pas utiliser cette machine virtuelle Windows Server, supprimez-la en effectuant les étapes suivantes :

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Choisissez votre groupe de ressources, par exemple *myResourceGroup*.
1. Sélectionnez votre machine virtuelle, par exemple *myVM*, puis sélectionnez **Supprimer**. Sélectionnez **Oui** pour confirmer la suppression de la ressource. La suppression de la machine virtuelle prend quelques minutes.
1. Une fois la machine virtuelle supprimée, sélectionnez le disque du système d’exploitation, la carte d’interface réseau et les autres ressources avec le préfixe *myVM-* , et supprimez-les.

## <a name="troubleshoot-domain-join-issues"></a>Résoudre les problèmes de jonction à un domaine

La machine virtuelle Windows Server doit être jointe avec succès au domaine managé Azure AD DS, de la même façon qu’un ordinateur local normal est jointe à un domaine Active Directory Domain Services. Si la machine virtuelle Windows Server ne peut pas se joindre au domaine managé Azure AD DS, cela indique qu’il existe un problème lié à la connectivité ou aux informations d’identification. Consultez les sections de dépannage suivantes pour effectuer avec succès la jonction au domaine managé.

### <a name="connectivity-issues"></a>Problèmes de connectivité

Si vous ne recevez pas d’invite demandant des informations d’identification pour la jonction au domaine, c’est qu’il y a un problème de connectivité. La machine virtuelle ne peut pas atteindre le domaine managé Azure AD DS sur le réseau virtuel.

Après avoir essayé chacune de ces étapes de dépannage, réessayez de joindre la machine virtuelle Windows Server au domaine managé.

* Vérifiez que la machine virtuelle est connectée au même réseau virtuel que celui où Azure AD DS est activé, ou qu’elle a une connexion réseau appairée.
* Effectuez un test Ping du nom de domaine DNS du domaine managé, par exemple `ping contoso.com`.
    * Si le test Ping échoue, essayez d’effectuer un test Ping des adresses IP du domaine managé, par exemple `ping 10.0.0.4`. L’adresse IP de votre environnement est affiché dans la page *Propriétés* quand vous sélectionnez le domaine managé Azure AD DS dans votre liste de ressources Azure.
    * Si le test ping de l’adresse IP aboutit contrairement à celui du domaine, il se peut que la fonction DNS ne soit pas correctement configurée. Vérifiez que les adresses IP du domaine managé sont configurées en tant que serveurs DNS pour le réseau virtuel.
* Essayez de vider le cache du résolveur DNS sur la machine virtuelle en utilisant la commande `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification

Si vous recevez une invite demandant des informations d’identification pour joindre le domaine, puis une erreur après avoir entré ces informations d’identification, c’est que la machine virtuelle est en mesure de se connecter au domaine managé Azure AD DS. Les informations d’identification que vous avez fournies n’autorisent pas la machine virtuelle à se joindre au domaine managé Azure AD DS.

Après avoir essayé chacune de ces étapes de dépannage, réessayez de joindre la machine virtuelle Windows Server au domaine managé.

* Vérifiez que le compte d’utilisateur que vous spécifiez appartient au groupe *Administrateurs AAD DC*.
* Essayez en utilisant le format UPN pour spécifier les informations d’identification, par exemple `contosoadmin@contoso.onmicrosoft.com`. S’il existe de nombreux utilisateurs avec le même préfixe UPN sur votre locataire, ou si votre préfixe UPN est très long, le *SAMAccountName* de votre compte peut être généré automatiquement. Dans ces cas-là, le format *SAMAccountName* de votre compte peut être différent de ce que vous attendez ou de ce que vous utilisez dans votre domaine local.
* Vérifiez que vous avez [activé la synchronisation de mot de passe][password-sync] avec votre domaine managé. Sans cette étape de configuration, les hachages de mot de passe nécessaires ne sont pas présents dans le domaine managé Azure AD DS pour authentifier correctement votre tentative de connexion.
* Attendez la fin de la synchronisation du mot de passe. Quand le mot de passe d’un compte d’utilisateur est changé, une synchronisation automatique en arrière-plan à partir d’Azure AD met à jour le mot de passe dans Azure AD DS. Il faut un certain temps pour que le mot de passe soit disponible pour être utilisé dans une jonction de domaine.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows Server
> * Se connecter à la machine virtuelle Windows Server sur un réseau virtuel Azure
> * Joindre la machine virtuelle au domaine managé Azure AD DS

Pour administrer votre domaine managé Azure AD DS, configurez une machine virtuelle de gestion en utilisant le Centre d’administration Active Directory.

> [!div class="nextstepaction"]
> [Installer des outils d’administration sur une machine virtuelle de gestion](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
