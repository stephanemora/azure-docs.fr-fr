---
title: Gérer DNS pour Azure AD Domain Services | Microsoft Docs
description: Découvrez comment installer les Outils du serveur DNS pour gérer le DNS pour un domaine managé Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613696"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrer DNS dans un domaine managé Azure AD Domain Services

Dans Azure Active Directory Domain Services (Azure AD DS), DNS est un composant clé. Azure AD DS inclut un serveur DNS qui fournit la résolution de noms pour le domaine managé. Ce serveur DNS comprend des enregistrements DNS intégrés et des mises à jour pour les composants clés qui permettent l’exécution du service.

Lorsque vous exécutez vos propres applications et services, il se peut que vous deviez créer des enregistrements DNS pour des machines qui ne sont pas jointes au domaine, ou configurer des adresses IP virtuelles pour des équilibreurs de charge ou des redirecteurs DNS externes. Les utilisateurs qui appartiennent au groupe *Administrateurs AAD DC* bénéficient de privilèges d’administration DNS sur le domaine managé Azure AD DS, et peuvent créer et modifier des enregistrements DNS personnalisés.

Dans un environnement hybride, les zones DNS et les enregistrements configurés dans un environnement AD DS local ne sont pas synchronisées avec Azure AD DS. Pour définir et utiliser vos propres entrées DNS, créez des enregistrements dans le serveur DNS Azure AD DS ou utilisez des redirecteurs conditionnels qui pointent vers des serveurs DNS existants dans votre environnement.

Cet article explique comment installer les Outils du serveur DNS, puis utiliser la console DNS pour gérer les enregistrements dans Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Avant de commencer

Pour faire ce qui est décrit dans cet article, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, suivez le tutoriel pour [créer et configurer une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Une machine virtuelle de gestion Windows Server jointe au domaine managé Azure AD DS.
    * Si nécessaire, suivez les étapes du tutoriel pour [créer et joindre une machine virtuelle Windows Server à un domaine managé][create-join-windows-vm].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="install-dns-server-tools"></a>Installer les Outils du serveur DNS

Pour créer et modifier des enregistrements DNS dans azure AD DS, vous devez installer les outils du serveur DNS. Ces outils peuvent être installés en tant que fonctionnalité de Windows Server. Pour plus d’informations sur l’installation des outils d’administration sur un client Windows, consultez [Installer les outils d’administration de serveur distant][install-rsat].

1. Connectez-vous à votre machine virtuelle de gestion. Pour connaître les différentes étapes vous permettant de vous connecter au portail Azure, consultez la page [Se connecter à une machine virtuelle Windows Server][connect-windows-server-vm].
1. Si **Gestionnaire de serveur** ne s’ouvre pas par défaut lorsque vous vous connectez à la machine virtuelle, sélectionnez le menu **Démarrer**, puis choisissez **Gestionnaire de serveur**.
1. Dans le volet *Tableau de bord* de la fenêtre **Gestionnaire de serveur**, sélectionnez **Ajouter des rôles et des fonctionnalités**.
1. Dans la page **Avant de commencer** de l’*Assistant Ajout de rôles et de fonctionnalités*, sélectionnez **Suivant**.
1. Pour le *Type d’installation*, laissez l’option **Installation basée sur un rôle ou une fonctionnalité** cochée et sélectionnez **Suivant**.
1. Dans la page **Sélection du serveur**, choisissez la machine virtuelle active dans le pool de serveurs, par exemple *myvm.aaddscontoso.com*, puis sélectionnez **Suivant**.
1. Sur la page **Rôles de serveurs**, cliquez sur **Suivant**.
1. Dans la page **Fonctionnalités**, développez le nœud **Outils d’administration de serveur distant**, puis développez le nœud **Outils d’administration de rôles**. Sélectionnez la fonctionnalité **Outils du serveur DNS** dans la liste Outils d’administration de rôles.

    ![Choisir d’installer les Outils du serveur DNS à partir de la liste des outils d’administration de rôles disponibles](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Dans la page **Confirmation**, sélectionnez **Installer**. L’installation des outils de gestion des stratégies de groupe peut prendre une ou deux minutes.
1. Une fois l’installation de la fonctionnalité terminée, sélectionnez **Fermer** pour quitter l’Assistant **Ajout de rôles et de fonctionnalités**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Ouvrir la console Gestion du service DNS pour administrer le serveur DNS

Une fois les Outils du serveur DNS installés, vous pouvez administrer les enregistrements DNS sur le domaine managé Azure AD DS.

> [!NOTE]
> Pour administrer DNS dans un domaine managé Azure AD DS, vous devez être connecté à un compte d’utilisateur membre du groupe *d’administrateurs AAD DDC*.

1. Dans l’écran d’accueil, sélectionnez **Outils d’administration**. Une liste des outils de gestion disponibles s’affiche, dont le **DNS** installé dans la section précédente. Sélectionnez **DNS** pour lancer la console Gestion du service DNS.
1. Dans la boîte de dialogue **Connexion au serveur DNS**, sélectionnez **L’ordinateur suivant**, puis entrez le nom de domaine DNS du domaine managé, par exemple *aaddscontoso.com* :

    ![Se connecter au domaine managé Azure AD DS dans la console DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. La console DNS se connecte au domaine managé Azure AD DS. Développez **Zones de recherche directe** ou **Zones de recherche inversée** pour créer vos entrées DNS requises ou modifier des enregistrements existants en fonction des besoins.

    ![Console DNS - Administration du domaine](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Lorsque vous gérez des enregistrements à l’aide des Outils du serveur DNS, veillez à ne pas supprimer ou modifier les enregistrements DNS intégrés qu’Azure AD DS utilise. Les enregistrements DNS intégrés incluent les enregistrements DNS de domaine, les enregistrements de serveur de noms et d’autres enregistrements utilisés pour le lieu du contrôleur de domaine. Si vous modifiez ces enregistrements, les services de domaine sont interrompus sur le réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion DNS, consultez l’article [Outils DNS](https://technet.microsoft.com/library/cc753579.aspx)sur Technet.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
