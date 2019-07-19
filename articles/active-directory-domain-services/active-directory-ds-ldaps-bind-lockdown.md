---
title: Créer une liaison à l’aide du protocole LDAP sécurisé (LDAPS) à un domaine managé Azure AD Domain Services | Microsoft Docs
description: Créer une liaison à un domaine managé Azure AD Domain Services à l’aide du protocole LDAP sécurisé (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: df0b3d27eec478280a33be831a2431eccdf05a74
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483387"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Créer une liaison à un domaine managé Azure AD Domain Services à l’aide du protocole LDAP sécurisé (LDAPS)

## <a name="before-you-begin"></a>Avant de commencer
Accomplissez la [Tâche 4 : Configurer DNS pour accéder au domaine managé à partir d’Internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Tâche 5 : Créer une liaison avec le domaine managé via LDAP à l’aide de LDP.exe
Vous pouvez utiliser l’outil LDP.exe inclus dans le package d’outils d’administration de serveur distant pour créer une liaison et effectuer des recherches via LDAP.

Tout d’abord, ouvrez LDP, puis connectez-vous au domaine managé. Cliquez sur **Connexion**, puis cliquez sur **Se connecter…** dans le menu. Spécifiez le nom de domaine DNS du domaine managé. Spécifiez le port à utiliser pour les connexions. Pour les connexions LDAP, utilisez le port 389. Pour les connexions LDAPS, utilisez le port 636. Cliquez sur le bouton **OK** pour vous connecter au domaine managé.

Ensuite, créez une liaison avec le domaine managé. Cliquez sur **Connexion**, puis cliquez sur **Lier…** dans le menu. Fournissez les informations d’identification d’un compte d’utilisateur appartenant au groupe Administrateurs AAD DC.

> [!IMPORTANT]
> Les utilisateurs (et comptes de service) ne peuvent pas effectuer des liaisons simples LDAP si vous avez désactivé la synchronisation de hachage de mot de passe NTLM sur votre instance Azure AD Domain Services.  Pour plus d’informations sur la désactivation de la synchronisation de hachage de mot de passe NTLM, consultez [Sécuriser votre domaine managé Azure AD DOmain Services](secure-your-domain.md).
>
>

Sélectionnez **Afficher**, puis sélectionnez **Arborescence** dans le menu. Laissez vide le champ Nom unique de base, puis cliquez sur OK. Accédez au conteneur dans lequel effectuer des recherches, cliquez dessus avec le bouton droit, puis sélectionnez Rechercher.

> [!TIP]
> - Les utilisateurs et les groupes synchronisés à partir d’Azure AD sont stockés dans l’unité d’organisation **Utilisateurs AAD**. Le chemin de recherche de cette unité d’organisation ressemble à ceci : ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Les comptes des ordinateurs joints à un domaine managé sont stockés dans l’unité d’organisation **Ordinateurs AAD DC**. Le chemin de recherche de cette unité d’organisation ressemble à ceci : ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Pour plus d’informations : [LDAP query basics](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter) (Concepts de base sur les requêtes LDAP)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Tâche 6 : Verrouiller l’accès LDAP sécurisé à votre domaine managé via Internet
> [!NOTE]
> Si vous n’avez pas activé l’accès LDAP sécurisé au domaine managé via Internet, ignorez cette étape de configuration.
>
>

Avant de commencer cette tâche, accomplissez les étapes de la [Tâche 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Lorsque vous activez l’accès LDAPS via Internet à votre domaine managé, cela crée une menace de sécurité. Le domaine managé est accessible à partir d’Internet sur le port utilisé pour le LDAP sécurisé (port 636). Vous pouvez donc choisir de restreindre l’accès au domaine managé à des adresses IP connues spécifiques. Créez un groupe de sécurité réseau (NSG) et associez-le au réseau virtuel dans lequel vous avez activé Azure AD Domain Services.

L’exemple de groupe de sécurité réseau dans le tableau suivant verrouille l’accès LDAP sécurisé via Internet. Les règles du groupe de sécurité réseau autorisent l’accès LDAP sécurisé entrant sur le port TCP 636 uniquement à partir d’un ensemble spécifique d’adresses IP. La règle « DenyAll » par défaut s’applique à tout autre trafic entrant en provenance d’internet. La règle de groupe de sécurité réseau pour autoriser l’accès LDAPS via Internet à partir d’adresses IP spécifiées prend le pas sur la règle DenyAll NSG.

![Exemple de groupe de sécurité réseau pour l’accès LDAP sécurisé via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Plus d’informations** - [Groupes de sécurité réseau](../virtual-network/security-overview.md).


## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de mise en route](create-instance.md)
* [Gérer un domaine Azure AD Domain Services](manage-domain.md)
* [LDAP query basics](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Gérer la stratégie de groupe pour Azure AD Domain Services](manage-group-policy.md)
* [Groupes de sécurité réseau](../virtual-network/security-overview.md)
* [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Étape suivante
[Résoudre les problèmes de protocole LDAP sécurisé sur un domaine managé](tshoot-ldaps.md)
