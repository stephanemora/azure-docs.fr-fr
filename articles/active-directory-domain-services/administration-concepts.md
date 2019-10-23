---
title: Concepts de gestion pour Azure AD Domain Services | Microsoft Docs
description: En savoir plus sur l’administration d’un domaine managé Azure Active Directory Domain Services et le comportement des comptes d’utilisateur et des mots de passe
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: b82927efa9054e71379d01993d1669527bc71402
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249411"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Concepts de gestion pour les comptes d’utilisateur, les mots de passe et l’administration dans Azure Active Directory Domain Services

Lorsque vous créez et exécutez un domaine managé Azure Active Directory Domain Services (AD DS), il existe des différences de comportement par rapport à un environnement AD DS local traditionnel. Vous utilisez les mêmes outils d’administration dans Azure AD DS en tant que domaine automanagé, mais vous ne pouvez pas accéder directement aux contrôleurs de domaine (DC). Il existe également des différences de comportement pour les stratégies de mot de passe et les hachages de mot de passe en fonction de la source de la création du compte d’utilisateur.

Cet article conceptuel explique en détail comment administrer un domaine managé Azure AD DS et le comportement différent des comptes d’utilisateur en fonction de la façon dont ils sont créés.

## <a name="domain-management"></a>Gestion de domaine

Dans Azure AD DS, les contrôleurs de domaine (DC) qui contiennent toutes les ressources telles que les utilisateurs et les groupes, les informations d’identification et les stratégies font partie du service managé. Pour la redondance, deux contrôleurs de domaine sont créés dans le cadre d’un domaine managé par Azure AD DS. Vous ne pouvez pas vous connecter à ces contrôleurs de domaine pour effectuer des tâches de gestion. Au lieu de cela, vous créez une machine virtuelle de gestion qui est jointe au domaine managé Azure AD DS , puis vous installez vos outils de gestion AD DS standard. Vous pouvez utiliser les composants logiciels enfichables du Centre d’administration Active Directory ou de MMC (Microsoft Management Console) comme les objets DNS ou de stratégie de groupe.

## <a name="user-account-creation"></a>Création d’un compte d'utilisateur

Les comptes d’utilisateurs peuvent être créés dans Azure AD DS de plusieurs façons. La plupart des comptes d’utilisateurs sont synchronisés à partir d’Azure AD, qui peuvent également inclure un compte d’utilisateur synchronisé à partir d’un environnement AD DS local. Vous pouvez également créer manuellement des comptes directement dans Azure AD DS. Certaines caractéristiques, telles que la synchronisation de mot de passe initiale ou la stratégie de mot de passe, se comportent différemment selon le mode et l’emplacement de création des comptes d’utilisateur.

* Le compte d’utilisateur peut être synchronisé à partir d’Azure AD. Cela comprend les comptes d’utilisateurs uniquement dans le cloud créés directement dans Azure AD, et les comptes d’utilisateur hybrides synchronisés à partir d’un environnement AD DS local à l’aide d’Azure AD Connect.
    * La majorité des comptes d’utilisateurs dans Azure AD DS sont créés par le biais du processus de synchronisation à partir d’Azure AD.
* Le compte d’utilisateur peut être créé manuellement dans un domaine managé Azure AD DS et n’existe pas dans Azure AD.
    * Si vous avez besoin de créer des comptes de service pour des applications qui s’exécutent uniquement dans Azure AD DS, vous pouvez les créer manuellement dans le domaine managé. Étant donné que la synchronisation est unidirectionnelle à partir d’Azure AD, les comptes d’utilisateurs créés dans Azure AD DS ne sont pas resynchronisés avec Azure AD.

## <a name="password-policy"></a>Stratégie de mot de passe

Azure AD DS comprend une stratégie de mot de passe par défaut qui définit des paramètres pour les éléments tels que le verrouillage de compte, l’âge maximum du mot de passe et la complexité du mot de passe. Les paramètres tels que la stratégie de verrouillage de compte s’appliquent à tous les utilisateurs dans Azure AD DS, quelle que soit la façon dont l’utilisateur a été créé comme indiqué dans la section précédente. Certains paramètres, comme la longueur minimale et la complexité du mot de passe, s’appliquent uniquement aux utilisateurs créés directement dans Azure AD DS.

Vous pouvez créer vos propres stratégies de mot de passe personnalisées pour remplacer la stratégie par défaut dans Azure AD DS. Ces stratégies personnalisées peuvent ensuite être appliquées à des groupes d’utilisateurs spécifiques en fonction des besoins.

Pour plus d’informations sur les différences dans la façon dont les stratégies de mot de passe sont appliquées en fonction de la source de la création de l’utilisateur, consultez [Stratégies de mot de passe et de verrouillage de compte sur les domaines managés][password-policy].

## <a name="password-hashes"></a>Hachages de mot de passe

Pour authentifier les utilisateurs sur le domaine managé, Azure AD DS nécessite les hachages de mot de passe dans un format adapté à l’authentification NTLM (NT LAN Manager) et Kerberos. Azure AD ne génère pas et ne stocke pas les hachages de mot de passe au format nécessaire pour l’authentification NTLM ou Kerberos tant que vous n’activez pas Azure AD DS pour votre locataire. Pour des raisons de sécurité, Azure AD ne stocke pas non plus d’informations d’identification de mot de passe sous forme de texte en clair. Par conséquent, Azure AD ne peut pas générer automatiquement ces hachages de mot de passe NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

Pour les comptes d’utilisateurs cloud uniquement, les utilisateurs doivent changer leur mot de passe avant de pouvoir utiliser Azure AD DS. Ce processus de changement du mot de passe entraîne la génération et le stockage dans Azure AD des hachages de mot de passe pour l’authentification Kerberos et NTLM.

Pour les utilisateurs synchronisés à partir d’un environnement AD DS local à l’aide d’Azure AD Connect, [activez la synchronisation des hachages de mot de passe ][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synchronise uniquement les hachages de mot de passe hérités lorsque vous activez Azure AD DS pour votre client Azure AD. Les hachages de mot de passe hérités ne sont pas utilisés si vous utilisez uniquement Azure AD Connect pour synchroniser un environnement AD DS local avec Azure AD.
>
> Si vos applications héritées n’utilisent pas l’authentification NTLM ou les liaisons simples LDAP, nous vous recommandons de désactiver la synchronisation de hachage de mot de passe NTLM pour Azure AD DS. Pour plus d’informations, consultez [Désactiver les suites de chiffrement faible et la synchronisation des hachages des informations d’identification NTLM][secure-domain].

Une fois configurés de façon appropriée, les hachages de mot de passe utilisables sont stockés dans le domaine managé Azure AD DS. Si vous supprimez le domaine managé Azure AD DS, tout hachage de mot de passe stocké à ce stade est également supprimé. Les informations d’identification synchronisées dans Azure AD ne peuvent pas être réutilisées si vous créez par la suite un domaine managé Azure AD DS : vous devez reconfigurer la synchronisation de hachage de mot de passe pour stocker à nouveau les hachages de mot de passe. Les machines virtuelles ou les utilisateurs auparavant joints à un domaine ne pourront pas s’authentifier immédiatement : Azure AD doit générer et stocker les hachages de mot de passe dans le nouveau domaine managé Azure AD DS. Pour plus d’informations, consultez [Processus de synchronisation du hachage de mot de passe pour Azure AD DS et Azure AD Connect][azure-ad-password-sync].

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, [créons un domaine managé Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
