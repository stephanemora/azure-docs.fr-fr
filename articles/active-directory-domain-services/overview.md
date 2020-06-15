---
title: Présentation des services de domaine Azure Active Directory | Microsoft Docs
description: Dans cette vue d’ensemble, découvrez ce qui est fourni par Azure Active Directory Domain Services et comment l’utiliser dans votre organisation pour fournir des services d’identité aux applications et services dans le cloud.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: c8a29f95e7b987af2b408ec24bc390a7fd162263
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554869"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Présentation d’Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) fournit des services de domaine managés, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP et l’authentification Kerberos/NTLM. Vous utilisez ces services de domaine sans avoir à déployer, gérer et apporter des correctifs aux contrôleurs de domaine dans le cloud.

Azure AD DS s’intègre à votre locataire Azure AD existant. Cette intégration permet aux utilisateurs de se connecter au service et aux applications connectés au domaine managé à l’aide de leurs informations d’identification existantes. Vous pouvez également utiliser des comptes d’utilisateurs et des groupes existants pour sécuriser l’accès aux ressources. Ces fonctionnalités fournissent une migration lift-and-shift plus simple des ressources locales vers Azure.

> [!div class="nextstepaction"]
> [Pour commencer, créez un domaine managé Azure AD DS à l’aide du portail Azure][tutorial-create].

Azure AD DS réplique les informations d’identité à partir d’Azure AD et fonctionne donc avec des clients Azure AD qui sont uniquement cloud ou synchronisés avec un environnement local Active Directory Domain Services (AD DS). Le même ensemble de fonctionnalités Azure AD DS existe pour les deux environnements.

* Si vous disposez d’un environnement AD DS local existant, vous pouvez synchroniser les informations des comptes d’utilisateurs pour fournir une identité cohérente aux utilisateurs. Pour en savoir plus, consultez [Synchronisation des objets et des informations d’identification dans un domaine managé][synchronization].
* Pour les environnements cloud uniquement, vous n’avez pas besoin d’un environnement AD DS local traditionnel pour utiliser les services d’identité centralisés d’Azure AD DS.

Pour découvrir comment administrer un domaine managé, consultez [Concepts de gestion pour les comptes d’utilisateur, les mots de passe et l’administration dans Azure AD DS][administration-concepts].

La vidéo suivante fournit une vue d’ensemble de la façon dont Azure AD DS s’intègre à vos applications et charges de travail pour fournir des services d’identité dans le cloud :

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Méthodes courantes pour fournir des solutions d’identité dans le cloud

Lorsque vous migrez des charges de travail existantes vers le cloud, les applications prenant en charge les répertoires peuvent utiliser LDAP pour l’accès en lecture ou en écriture à un répertoire AD DS local. Les applications s’exécutant sous Windows Server sont généralement déployées sur des machines virtuelles jointes à un domaine, et peuvent être gérées de façon sécurisée à l’aide de la stratégie de groupe. Pour authentifier les utilisateurs finaux, les applications peuvent également s’appuyer sur l’authentification intégrée de Windows, telle que l’authentification Kerberos ou NTLM.

Les administrateurs informatiques utilisent souvent l’une des solutions suivantes pour fournir un service d’identité aux applications qui s’exécutent dans Azure :

* Configurez une connexion VPN de site à site entre les charges de travail qui s’exécutent dans Azure et un environnement AD DS local.
    * Les contrôleurs de domaine locaux fournissent ensuite l’authentification par le biais de la connexion VPN.
* Créez des contrôleurs de domaine de réplication à l’aide de machines virtuelles Azure pour étendre le domaine ou la forêt AD DS à partir de l’environnement local.
    * Les contrôleurs de domaine qui s’exécutent sur des machines virtuelles Azure assurent l’authentification et répliquent les informations d’annuaire à partir de l’environnement AD DS local.
* Déployez un environnement AD DS autonome dans Azure à l’aide de contrôleurs de domaine qui s’exécutent sur des machines virtuelles Azure.
    * Les contrôleurs de domaine qui s’exécutent sur des machines virtuelles Azure assurent l’authentification, mais aucune information d’annuaire n’est répliquée à partir d’un environnement AD DS local.

Grâce à ces approches, les connexions VPN au répertoire local rendent les applications vulnérables aux problèmes ou pannes réseau temporaires. Si vous déployez des contrôleurs de domaine à l’aide de machines virtuelles dans Azure, l’équipe informatique doit gérer les machines virtuelles, puis sécuriser, corriger, superviser, sauvegarder et dépanner les contrôleurs de domaine.

Azure AD DS offre des alternatives à la nécessité de créer des connexions VPN dans un environnement AD DS local ou d’exécuter et de gérer des machines virtuelles dans Azure pour fournir des services d’identité. En tant que service managé, Azure AD DS réduit la complexité de la création d’une solution d’identité intégrée pour les environnements hybrides et cloud uniquement.

> [!div class="nextstepaction"]
> [Comparer Azure AD DS avec Azure AD et AD DS automanagé sur des machines virtuelles Azure ou en local][compare]

## <a name="how-does-azure-ad-ds-work"></a>Fonctionnement d’Azure AD DS

Pour fournir des services d’identité, Azure crée une instance AD DS sur un réseau virtuel de votre choix. En arrière-plan, une paire de contrôleurs de domaine Windows Server est créée et s’exécute sur les machines virtuelles Azure. Vous n’avez pas besoin de gérer, configurer ou mettre à jour ces contrôleurs de domaine. La plateforme Azure gère les contrôleurs de domaine dans le cadre du service Azure AD DS.

Le domaine managé Azure AD DS est configuré pour effectuer une synchronisation unidirectionnelle à partir d’Azure AD afin de fournir l’accès à un ensemble central d’utilisateurs, de groupes et d’informations d’identification. Vous pouvez créer des ressources directement dans le domaine managé Azure AD DS, mais elles ne sont pas resynchronisées sur Azure AD. Dans Azure, les applications, les services et les machines virtuelles qui se connectent à ce réseau virtuel peuvent ensuite utiliser des fonctionnalités AD DS courantes telles que la jonction de domaine, la stratégie de groupe, LDAP et l’authentification Kerberos/NTLM.

Dans un environnement hybride avec un environnement AD DS local, [Azure AD Connect][azure-ad-connect] synchronise les informations d’identité avec Azure AD, qui sont ensuite synchronisées avec Azure AD DS.

![Synchronisation dans Azure AD Domain Services avec Azure AD et Active Directory Domain Services en local à l’aide d’AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Pour voir Azure AD DS en action, étudions quelques exemples :

* [Azure AD DS pour les organisations hybrides](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS pour les organisations cloud uniquement](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS pour les organisations hybrides

De nombreuses organisations exécutent une infrastructure hybride qui comprend des charges de travail d’application cloud et locales. Les applications héritées migrées vers Azure dans le cadre d’une stratégie lift-and-shift peuvent utiliser des connexions LDAP traditionnelles pour fournir des informations d’identité. Pour prendre en charge cette infrastructure hybride, les informations d’identité d’un environnement AD DS local peuvent être synchronisées avec un locataire Azure AD. Azure AD DS peut ensuite fournir ces applications héritées dans Azure avec une source d’identités, sans qu’il soit nécessaire de configurer ni de gérer la connectivité des applications vers les services d’annuaire locaux.

Examinons un exemple de Litware Corporation, une organisation hybride qui exécute des ressources locales et Azure :

![Azure Active Directory Domain Services pour une organisation hybride qui inclut une synchronisation locale](./media/overview/synced-tenant.png)

* Les applications et charges de travail de serveurs nécessitant des services de domaine sont déployées dans un réseau virtuel dans Azure.
    * Cela peut inclure des applications héritées migrées vers Azure dans le cadre d’une stratégie lift-and-shift.
* Afin de synchroniser les informations d’identité du répertoire local avec le locataire Azure AD, la société Litware Corporation a déployé [Azure AD Connect][azure-ad-connect].
    * Les informations d’identité incluent les comptes d’utilisateurs et les appartenances aux groupes.
* L’équipe informatique de Litware active Azure AD DS pour son locataire Azure AD dans ce réseau virtuel ou un réseau virtuel homologué.
* Les applications et machines virtuelles déployées dans le réseau virtuel Azure peuvent ensuite utiliser des fonctionnalités Azure AD DS telles que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos et la stratégie de groupe.

> [!IMPORTANT]
> Azure AD Connect doit uniquement être installé et configuré pour la synchronisation avec des environnements AD DS locaux. L’installation d’Azure AD Connect n’est pas prise en charge dans un domaine managé Azure AD DS pour resynchroniser des objets sur Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS pour les organisations cloud uniquement

Un locataire cloud uniquement Azure AD ne dispose pas d’une source d’identité locale. Par exemple, les comptes d’utilisateur et les appartenances aux groupes sont créés et gérés directement dans Azure AD.

Examinons maintenant un exemple pour Contoso, une organisation cloud uniquement qui utilise Azure AD pour l’identité. Toutes les identités des utilisateurs, leurs informations d’identification et les appartenances aux groupes sont créées et gérées dans Azure AD. Il n’existe aucune configuration supplémentaire d’Azure AD Connect pour synchroniser des informations d’identité à partir d’un répertoire local.

![Azure Active Directory Domain Services pour une organisation cloud uniquement sans synchronisation locale](./media/overview/cloud-only-tenant.png)

* Les applications et charges de travail de serveurs nécessitant des services de domaine sont déployées dans un réseau virtuel dans Azure.
* L’équipe informatique de Contoso active Azure AD DS pour son locataire Azure AD dans ce réseau virtuel ou un réseau virtuel homologué.
* Les applications et machines virtuelles déployées dans le réseau virtuel Azure peuvent ensuite utiliser des fonctionnalités Azure AD DS telles que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos et la stratégie de groupe.

## <a name="azure-ad-ds-features-and-benefits"></a>Fonctionnalités et avantages d’Azure AD DS

Pour fournir des services d’identité aux applications et aux machines virtuelles dans le cloud, Azure AD DS est entièrement compatible avec un environnement AD DS traditionnel pour des opérations telles que la jonction de domaine, le protocole LDAP sécurisé (LDAPS), la stratégie de groupe, la gestion de DNS ainsi que la prise en charge des lectures et liaisons LDAP. La prise en charge de l’écriture LDAP est disponible pour les objets créés dans le domaine Azure AD DS managé, mais pas pour les ressources synchronisées à partir d’Azure AD.

Pour en savoir plus sur vos options d’identité, consultez [Comparer Azure AD DS avec Azure AD, Active Directory Domain Services sur des machines virtuelles Azure et Active Directory Domain Services en local][compare].

Les fonctionnalités suivantes d’Azure AD DS simplifient les opérations de déploiement et de gestion :

* **Expérience de déploiement simplifiée :** Azure AD DS est activé pour votre locataire Azure AD à l’aide d’un seul Assistant dans le Portail Azure.
* **Intégration à Azure AD :** Les comptes d’utilisateur, les appartenances aux groupes et les informations d’identification sont automatiquement disponibles à partir de votre locataire Azure AD. Les nouveaux utilisateurs, groupes ou modifications apportées aux attributs à partir de votre locataire Azure AD ou de votre environnement AD DS local sont automatiquement synchronisés avec Azure AD DS.
    * Les comptes d’annuaires externes liés à votre annuaire Azure AD ne sont pas disponibles dans Azure AD DS. Les informations d’identification ne sont pas disponibles pour ces annuaires externes et ne peuvent donc pas être synchronisées dans un domaine managé par Azure AD DS.
* **Utilisation de vos informations d’identification/mots de passe d’entreprise :** Les mots de passe des utilisateurs dans Azure AD DS sont identiques à ceux dans votre locataire Azure AD. Les utilisateurs peuvent utiliser leurs informations d’identification d’entreprise pour joindre des machines à des domaines, se connecter de manière interactive ou via le bureau à distance et s’authentifier sur le domaine managé Azure AD DS.
* **Authentification NTLM et Kerberos :** Grâce à la prise en charge de l’authentification NTLM et Kerberos, vous pouvez déployer des applications qui reposent sur l’authentification intégrée de Windows.
* **Haute disponibilité :** Azure AD DS inclut plusieurs contrôleurs de domaine, qui fournissent une haute disponibilité pour votre domaine managé. Cette haute disponibilité garantit le temps de fonctionnement du service et la résilience aux défaillances.
    * Dans les régions qui prennent en charge les [Zones de disponibilité Azure][availability-zones], ces contrôleurs de domaine sont également répartis entre les zones pour une résilience supplémentaire.

Voici quelques aspects majeurs d’un domaine managé Azure AD DS :

* Le domaine managé Azure AD DS est un domaine autonome et non une extension du domaine local.
    * Si nécessaire, vous pouvez créer des approbations de forêt sortantes unidirectionnelles entre Azure AD DS et un environnement AD DS local. Pour plus d’informations, consultez [Concepts et fonctionnalités de la forêt de ressources pour Azure AD DS][ forest-trusts].
* Votre équipe informatique n’a pas à gérer, à appliquer les correctifs ni à analyser les contrôleurs de domaine pour ce domaine managé Azure AD DS.

Pour les environnements hybrides qui exécutent AD DS localement, vous n’avez pas besoin de gérer la réplication Active Directory sur le domaine managé Azure AD DS. Les comptes d’utilisateur, les appartenances aux groupes et les informations d’identification de votre annuaire local sont synchronisés avec Azure AD via [Azure AD Connect][azure-ad-connect]. Ces comptes utilisateur, appartenances aux groupes et informations d’identification sont automatiquement disponibles dans le domaine managé Azure AD DS.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les différentes entre Azure AD DS et d’autres solutions d’identité et sur le fonctionnement de la synchronisation, consultez les articles suivants :

* [Comparer Azure AD DS avec Azure AD, Active Directory Domain Services sur des machines virtuelles Azure et Active Directory Domain Services en local][compare]
* [Comprendre comment la solution Azure AD Domain Services est synchronisée avec votre répertoire Azure AD][synchronization]

Pour bien démarrer, [créez un domaine managé Azure AD DS à l’aide du portail Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
