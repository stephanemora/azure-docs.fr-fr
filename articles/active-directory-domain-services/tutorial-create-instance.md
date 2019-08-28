---
title: Tutoriel - Créer une instance Azure Active Directory Domain Services | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment créer et configurer une instance Azure Active Directory Domain Services avec le portail Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: cc062f830facb0d617dc649ecd17acfff0a740af
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69618929"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Didacticiel : Créer et configurer une instance Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) fournit des services de domaine managés, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP, et l’authentification Kerberos/NTLM entièrement compatible avec Windows Server Active Directory. Vous consommez ces services de domaine sans déployer, gérer et mettre à jour avec des correctifs les contrôleurs de domaine vous-même. Azure AD DS s’intègre à votre locataire Azure AD existant. Cette intégration permet aux utilisateurs de se connecter en utilisant leurs informations d’identification d’entreprise, et vous pouvez utiliser des groupes et des comptes d’utilisateur existants pour sécuriser l’accès aux ressources.

Ce tutoriel vous montre comment créer et configurer une instance Azure AD DS avec le portail Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer les paramètres de réseau virtuel et DNS pour un domaine managé
> * Créer une instance Azure AD DS
> * Ajouter des utilisateurs d’administration à la gestion du domaine
> * Activer la synchronisation de hachage de mot de passe

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Vous devez disposer des privilèges d’*Administrateur global* dans votre locataire Azure AD pour activer Azure AD DS.
* Vous avez besoin de privilèges de *Contributeur* dans votre abonnement Azure pour créer les ressources Azure AD DS nécessaires.
* Le locataire Azure AD doit être [configuré pour la réinitialisation du mot de passe en libre-service][configure-sspr].

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans ce tutoriel, vous créez et vous configurez une instance Azure AD DS avec le portail Azure. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Créer une instance et configurer les paramètres de base

Pour lancer l’Assistant **Activer Azure AD Domain Services**, procédez comme suit :

1. Dans le coin supérieur gauche du portail Azure, sélectionnez **+ Créer une ressource**.
1. Entrez *Domain Services* dans la barre de recherche, puis choisissez *Azure AD Domain Services* dans les suggestions de recherche.
1. Dans la page Azure AD Domain Services, cliquez sur le bouton **Créer**. L’Assistant **Activer Azure AD Domain Services** est lancé.

Quand vous créez une instance Azure AD DS, vous spécifiez un nom DNS. Voici quelques considérations liées au choix de ce nom DNS :

* **Nom de domaine intégré :** Par défaut, le nom de domaine intégré de l’annuaire est utilisé (un suffixe *.onmicrosoft.com*). Si vous voulez activer l’accès LDAP sécurisé au domaine managé via Internet, vous ne pouvez pas créer un certificat numérique pour sécuriser la connexion avec ce domaine par défaut. Microsoft détient le domaine *.onmicrosoft.com* : une autorité de certification n’émettra donc pas de certificat.
* **Noms de domaine personnalisés :** L’approche la plus courante consiste à spécifier un nom de domaine personnalisé, en général celui que vous possédez déjà et qui est routable. Quand vous utilisez un domaine personnalisé routable, le trafic peut s’écouler correctement en fonction des besoins pour prendre en charge vos applications.
* **Suffixes de domaine non routables :** D’une façon générale, nous vous recommandons d’éviter un suffixe de nom de domaine non routable, comme *contoso.local*. Le suffixe *.local* n’est pas routable et peut entraîner des problèmes de résolution DNS.

Les restrictions de nom DNS suivantes s’appliquent également :

* **Restrictions de préfixe de domaine :** Vous ne pouvez pas créer de domaine managé avec un préfixe de plus de 15 caractères. Le préfixe du nom de domaine spécifié (par exemple, *contoso* dans le nom de domaine *contoso.com*) doit contenir au maximum 15 caractères.
* **Conflits de noms de réseau :** Le nom de domaine DNS de votre domaine managé ne doit pas déjà exister dans le réseau virtuel. En particulier, recherchez les scénarios suivants, qui aboutiraient à un conflit de noms :
    * Si vous avec un domaine Active Directory avec le même nom de domaine DNS sur le réseau virtuel Azure.
    * Si le réseau virtuel dans lequel vous envisagez d’activer le domaine managé a une connexion VPN avec votre réseau local. Dans ce scénario, veillez à ne pas avoir de domaine portant le même nom de domaine DNS sur votre réseau local.
    * Si vous avez un service cloud Azure avec ce nom sur le réseau virtuel Azure.

Renseignez les champs de la fenêtre *De base* du portail Azure pour créer une instance Azure AD DS :

1. Entrez un **Nom de domaine DNS** pour votre domaine managé, en tenant compte des points précédents.
1. Sélectionnez l’**Abonnement** Azure dans lequel vous souhaitez créer le domaine managé.
1. Sélectionnez le **Groupe de ressources** auquel le domaine managé doit appartenir. Choisissez de **Créer** ou de sélectionner un groupe de ressources existant.
1. Choisissez l’**Emplacement** Azure dans lequel créer le domaine managé.
1. Cliquez sur **OK** pour accéder à la section **Réseau**.

![Configurer les paramètres de base pour une instance Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>Créer et configurer le réseau virtuel

Pour fournir une connectivité, un réseau virtuel Azure et un sous-réseau dédié sont nécessaires. Azure AD DS est activé dans ce sous-réseau du réseau virtuel. Dans ce tutoriel, vous créez un réseau virtuel, mais vous pouvez aussi choisir d’utiliser un réseau virtuel existant. Dans les deux approches, vous devez créer un sous-réseau dédié qui sera utilisé par Azure AD DS.

Voici certains éléments à prendre en compte pour ce sous-réseau de réseau virtuel dédié :

* Le sous-réseau doit avoir au moins 3 à 5 adresses IP disponibles dans sa plage d’adresses pour prendre en charge les ressources Azure AD DS.
* Ne sélectionnez pas le sous-réseau de *passerelle* pour le déploiement d’Azure AD DS. Le déploiement d’Azure AD DS dans un sous-réseau de *passerelle* n’est pas pris en charge.
* Ne déployez pas d’autres machines virtuelles sur le sous-réseau. Les applications et les machines virtuelles utilisent souvent des groupes de sécurité réseau pour sécuriser la connectivité. L’exécution de ces charges de travail dans un sous-réseau distinct vous permet d’appliquer ces groupes de sécurité réseau sans interrompre la connexion à votre domaine managé.
* Vous ne pouvez pas déplacer votre domaine managé vers un autre réseau virtuel une fois Azure AD DS activé.

Pour plus d’informations sur la planification et la configuration du réseau virtuel, consultez [Considérations relatives au réseau pour Azure Active Directory Domain Services][network-considerations].

Renseignez les champs de la fenêtre *Réseau* comme suit :

1. Dans la fenêtre **Réseau**, choisissez **Sélectionner un réseau virtuel**.
1. Pour ce tutoriel, choisissez de **Créer** un réseau virtuel où déployer Azure AD DS.
1. Entrez un nom pour le réseau virtuel, par exemple *myVnet*, puis spécifiez une plage d’adresses, par exemple *10.1.0.0/16*.
1. Créez un sous-réseau dédié avec un nom explicite, par exemple *DomainServices*. Spécifiez une plage d’adresses, par exemple *10.1.0.0/24*.

    ![Créer un réseau virtuel et un sous-réseau pour une utilisation avec Azure AD Domain Services](./media/tutorial-create-instance/create-vnet.png)

    Veillez à choisir une plage d’adresses qui se trouve dans votre plage d’adresses IP privée. Les plages d’adresses IP qui ne vous appartiennent pas et qui se trouvent dans l’espace d’adressage public provoquent des erreurs dans Azure AD DS.

    > [!TIP]
    > La page **Choisir un réseau virtuel** montre les réseaux virtuels existants qui appartiennent au groupe de ressources et à l’emplacement Azure que vous avez précédemment sélectionnés. Vous devez [créer un sous-réseau dédié][create-dedicated-subnet] avant de déployer Azure AD DS.

1. Une fois le réseau virtuel et le sous-réseau créés, le sous-réseau doit être sélectionné automatiquement, par exemple *DomainServices*. Vous pouvez aussi choisir un autre sous-réseau existant qui fait partie du réseau virtuel sélectionné :

    ![Choisir le sous-réseau dédié au sein du réseau virtuel](./media/tutorial-create-instance/choose-subnet.png)

1. Sélectionnez **OK** pour confirmer la configuration du réseau virtuel.

## <a name="configure-an-administrative-group"></a>Configurer le groupe d’administration

Un groupe d’administration spécial nommé *Administrateurs AAD DC* est utilisé pour la gestion du domaine Azure AD DS. Les membres de ce groupe bénéficient d’autorisations d’administration sur les machines virtuelles jointes au domaine managé. Sur les machines virtuelles jointes au domaine, ce groupe est ajouté au groupe des administrateurs locaux. Les membres de ce groupe peuvent aussi utiliser Bureau à distance pour se connecter à distance aux machines virtuelles jointes au domaine.

Vous ne disposez pas des autorisations *Administrateur de domaine* ou *Administrateur d’entreprise* sur un domaine managé avec Azure AD DS. Ces autorisations sont réservées par le service et ne sont pas disponibles pour les utilisateurs au sein du locataire. Au lieu de cela, le groupe *Administrateurs AAD DC* vous permet d’effectuer des opérations privilégiées. Ces opérations comprennent la jonction d’ordinateurs au domaine, l’appartenance au groupe d’administration sur les machines virtuelles jointes au domaine, et la configuration de la stratégie de groupe.

L’Assistant crée automatiquement le groupe *Administrateurs AAD DC* dans votre annuaire Azure AD. Si vous disposez d’un groupe du même nom dans votre répertoire Azure AD, l’Assistant sélectionne ce groupe. Vous pouvez aussi choisir d’ajouter des utilisateurs supplémentaires à ce groupe *Administrateurs AAD DC* lors du processus de déploiement. Ces étapes peuvent être effectuées ultérieurement.

1. Pour ajouter des utilisateurs supplémentaires à ce groupe *Administrateurs AAD DC*, sélectionnez **Gérer l’appartenance au groupe**.
1. Sélectionnez le bouton **Ajouter des membres**, puis recherchez et sélectionnez les utilisateurs dans votre annuaire Azure AD. Par exemple, recherchez votre propre compte, puis ajoutez-le au groupe *Administrateurs AAD DC*.

    ![Configurer l’appartenance au groupe, du groupe Administrateurs AAD DC](./media/tutorial-create-instance/admin-group.png)

1. Quand vous avez terminé, sélectionnez **OK**.

## <a name="configure-synchronization"></a>Configurer la synchronisation

Azure AD DS vous permet de synchroniser *tous* les utilisateurs et les groupes disponibles dans Azure AD, ou d’effectuer une synchronisation *limitée* seulement à des groupes spécifiques. Si vous choisissez de synchroniser *tous* les utilisateurs et groupes, vous ne pourrez pas choisir ultérieurement d’effectuer seulement une synchronisation limitée. Pour en savoir plus sur la synchronisation limitée, consultez [Synchronisation limitée d’Azure AD Domain Services][scoped-sync].

1. Pour ce tutoriel, choisissez de synchroniser **Tous** les utilisateurs et groupes. Ce choix de synchronisation est l’option par défaut.

    ![Effectuer une synchronisation complète des utilisateurs et des groupes à partir d’Azure AD](./media/tutorial-create-instance/sync-all.png)

1. Sélectionnez **OK**.

## <a name="deploy-your-managed-domain"></a>Déployer votre domaine managé

Dans la page **Résumé** de l’Assistant, examinez les paramètres de configuration du domaine managé. Vous pouvez revenir à n’importe quelle étape de l’Assistant pour faire des modifications.

1. Pour créer le domaine managé, sélectionnez **OK**.
1. Le processus d’approvisionnement de votre domaine managé peut prendre jusqu’à une heure. Vous voyez une notification dans le portail indiquant la progression de votre déploiement Azure AD DS. Sélectionnez la notification pour voir la progression détaillée du déploiement.

    ![Notification dans le portail Azure du déploiement en cours](./media/tutorial-create-instance/deployment-in-progress.png)

1. Sélectionnez votre groupe de ressources, par exemple *myResourceGroup*, puis choisissez votre instance Azure AD DS dans la liste des ressources Azure, par exemple *contoso.com*. L’onglet **Vue d’ensemble** montre que le domaine managé est actuellement en cours de *Déploiement*. Vous ne pouvez pas configurer le domaine managé tant qu’il n’est pas entièrement provisionné.

    ![État des services de domaine pendant l’état Provisionnement](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Lorsque le domaine managé est entièrement approvisionné, l’onglet **Vue d’ensemble** affiche l’état du domaine comme *En cours d’exécution*.

    ![État des services de domaine une fois le provisionnement terminé](./media/tutorial-create-instance/successfully-provisioned.png)

Pendant le processus de provisionnement, Azure AD DS crée deux applications d’entreprise nommées *Services de contrôleur de domaine* et *AzureActiveDirectoryDomainControllerServices* dans votre annuaire. Ces applications d’entreprise sont nécessaires pour entretenir votre domaine géré. Il est impératif de ne pas supprimer ces applications, à aucun moment.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Mettre à jour les paramètres DNS pour le réseau virtuel Azure

Avec Azure AD DS correctement déployé, configurez maintenant le réseau virtuel pour permettre à d’autres machines virtuelles et applications connectées d’utiliser le domaine managé. Pour fournir cette connectivité, mettez à jour les paramètres du serveur DNS pour que votre réseau virtuel pointe vers les deux adresses IP où Azure AD DS est déployé.

1. L’onglet **Vue d’ensemble** pour votre domaine managé montre quelques **Étapes de configuration obligatoires**. La première étape de configuration est de mettre à jour les paramètres du serveur DNS pour votre réseau virtuel. Une fois les paramètres DNS correctement configurés, cette étape n’apparaît plus.

    Les adresses listées sont les contrôleurs de domaine à utiliser dans le réseau virtuel. Dans cet exemple, ces adresses sont *10.1.0.4* et *10.1.0.5*. Vous pouvez trouver ultérieurement ces adresses IP sous l’onglet **Propriétés**.

    ![Configurer les paramètres DNS pour votre réseau virtuel avec les adresses IP d’Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Pour mettre à jour les paramètres du serveur DNS pour le réseau virtuel, sélectionnez le bouton **Configurer**. Les paramètres DNS sont configurés automatiquement pour votre réseau virtuel.

> [!TIP]
> Si vous avez sélectionné un réseau virtuel existant au cours des étapes précédentes, les machines virtuelles connectées au réseau obtiennent les nouveaux paramètres DNS seulement après un redémarrage. Vous pouvez redémarrer les machines virtuelles en utilisant le portail Azure, Azure PowerShell ou Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Activer les comptes d’utilisateur pour Azure AD DS

Pour authentifier les utilisateurs sur le domaine managé, Azure AD DS nécessite les hachages de mot de passe dans un format adapté à l’authentification NTLM (NT LAN Manager) et Kerberos. Azure AD ne génère pas et ne stocke pas les hachages de mot de passe au format nécessaire pour l’authentification NTLM ou Kerberos tant que vous n’activez pas Azure AD DS pour votre locataire. Pour des raisons de sécurité, Azure AD ne stocke pas non plus d’informations d’identification de mot de passe sous forme de texte en clair. Par conséquent, Azure AD ne peut pas générer automatiquement ces hachages de mot de passe NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [!NOTE]
> Une fois configurés de façon appropriée, les hachages de mot de passe utilisables sont stockés dans le domaine managé Azure AD DS. Si vous supprimez le domaine managé Azure AD DS, tout hachage de mot de passe stocké à ce stade est également supprimé. Les informations d’identification synchronisées dans Azure AD ne peuvent pas être réutilisées si vous créez par la suite un domaine managé Azure AD DS : vous devez reconfigurer la synchronisation de hachage de mot de passe pour stocker à nouveau les hachages de mot de passe. Les machines virtuelles ou les utilisateurs auparavant joints à un domaine ne pourront pas s’authentifier immédiatement : Azure AD doit générer et stocker les hachages de mot de passe dans le nouveau domaine managé Azure AD DS. Pour plus d’informations, consultez [Processus de synchronisation du hachage de mot de passe pour Azure AD DS et Azure AD Connect][password-hash-sync-process].

Les étapes de génération et de stockage de ces hachages de mot de passe sont différentes pour les comptes d’utilisateur cloud uniquement créés dans Azure AD et pour les comptes d’utilisateur qui sont synchronisés à partir de votre annuaire local avec Azure AD Connect. Un compte d’utilisateur uniquement dans le cloud est un compte qui a été créé dans votre répertoire Azure AD à l’aide du portail Azure ou d’applets de commande PowerShell Azure AD. Ces comptes d’utilisateurs ne sont pas synchronisés à partir d’un annuaire local. Dans ce tutoriel, nous utilisons un compte d’utilisateur de base cloud uniquement. Pour plus d’informations sur les étapes supplémentaires nécessaires pour utiliser Azure AD Connect, consultez [Synchroniser les hachages de mot de passe pour les comptes d’utilisateur synchronisés à partir de votre annuaire Active Directory local vers votre domaine managé][on-prem-sync].

> [!TIP]
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs cloud uniquement et d’utilisateurs provenant de votre annuaire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.

Pour les comptes d’utilisateurs cloud uniquement, les utilisateurs doivent changer leur mot de passe avant de pouvoir utiliser Azure AD DS. Ce processus de changement du mot de passe entraîne la génération et le stockage dans Azure AD des hachages de mot de passe pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs du locataire qui doivent utiliser Azure AD DS, ce qui force le changement de mot de passe à la connexion suivante, ou bien demander à ces utilisateurs de changer manuellement leur mot de passe. Pour ce tutoriel, nous changeons manuellement le mot de passe d’un utilisateur.

Pour qu’un utilisateur puisse réinitialiser son mot de passe, le locataire Azure AD doit être [configuré pour la réinitialisation du mot de passe en libre-service][configure-sspr].

Pour changer le mot de passe d’un utilisateur cloud uniquement, l’utilisateur doit effectuer les étapes suivantes :

1. Accédez à la page Panneau d’accès Azure AD à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. En haut à droite, sélectionnez votre nom, puis choisissez **Profil** dans le menu déroulant.

    ![Sélectionner un profil](./media/tutorial-create-instance/select-profile.png)

1. Dans la page **Profil**, sélectionnez **Changer le mot de passe**.
1. Dans la page **Changer le mot de passe**, entrez votre mot de passe existant (l’ancien), puis entrez un nouveau mot de passe et confirmez-le.
1. Sélectionnez **Envoyer**.

Une fois que vous avez changé votre mot de passe, quelques minutes sont nécessaires pour que le nouveau mot de passe soit utilisable dans Azure AD DS. Au bout de 20 minutes environ, vous pouvez utiliser le nouveau mot de passe pour vous connecter aux ordinateurs joints au domaine managé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer les paramètres de réseau virtuel et DNS pour un domaine managé
> * Créer une instance Azure AD DS
> * Ajouter des utilisateurs d’administration à la gestion du domaine
> * Activer les comptes d’utilisateur pour Azure AD DS et générer les hachages de mot de passe

Pour voir ce domaine managé en action, créez et joignez une machine virtuelle au domaine.

> [!div class="nextstepaction"]
> [Joindre une machine virtuelle Windows Server à votre domaine managé](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
