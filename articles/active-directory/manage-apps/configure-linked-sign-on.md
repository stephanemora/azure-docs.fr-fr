---
title: Configurer l’authentification liée dans Azure Active Directory
description: Configurez l’authentification liée dans Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 2269a8f7f58d35fee5e2ca30a77af5e8cba83678
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459332"
---
# <a name="configure-linked-sign-on"></a>Configurer l’authentification liée

Avec la [série de guides de démarrage rapide](view-applications-portal.md) sur la gestion des applications, vous avez appris à utiliser Azure AD comme fournisseur d’identité (IdP) pour une application. Dans le guide de démarrage rapide, vous configurez l’authentification unique SAML. Une autre option consiste à utiliser **Authentification liée**. Cet article décrit plus en détail l’option d’authentification liée.

L’option **Authentification liée** vous permet de configurer l’emplacement cible lorsqu’un utilisateur sélectionne l’application dans le portail [Mes applications](https://myapplications.microsoft.com/) ou Office 365 de votre organisation.

Voici quelques scénarios courants dans lesquels l’option d’authentification liée est utile :
- Ajouter un lien vers une application web personnalisée qui utilise actuellement la fédération, par exemple les services de fédération Active Directory (AD FS).
- Ajouter des liens ciblés vers des pages SharePoint spécifiques ou d’autres pages web dont vous souhaitez qu’elles s’affichent uniquement sur vos volets d’accès d’utilisateurs.
- Ajoutez un lien vers une application qui ne requiert pas d’authentification. 
 
 L’option **Authentification liée** ne fournit pas de fonctionnalité d’authentification via des informations d’identification Azure AD. Toutefois, vous pouvez toujours utiliser les autres fonctionnalités d’**Applications d’entreprise**. Par exemple, vous pouvez utiliser les journaux d’audit et ajouter un logo et un nom d’application personnalisés.

## <a name="before-you-begin"></a>Avant de commencer

Pour être efficace rapidement, suivez la [série de guides de démarrage rapide](view-applications-portal.md) sur la gestion des applications. Dans le démarrage rapide, où vous configurez l’authentification unique, vous trouverez également l’option **Authentification liée**. 

L’option **Authentification liée** ne fournit pas de fonctionnalité d’authentification via Azure AD. L’option définit simplement l’emplacement auquel les utilisateurs sont envoyés lorsqu’ils sélectionnent l’application sur [Mes applications](https://myapplications.microsoft.com/) ou le lanceur d’applications Microsoft 365.

> [!IMPORTANT] 
> Dans certains cas, l’option **Authentification unique** n’est pas accessible pour une application sous **Applications d’entreprise**. 
>
> Si l’application a été inscrite à l’aide d’**Inscriptions d’applications**, la fonctionnalité d’authentification unique est configurée pour utiliser OIDC OAuth par défaut. Dans ce cas, l’option **Authentification unique** ne s’affiche pas dans le volet de navigation sous **Applications d’entreprise**. Quand vous utilisez **inscriptions d’applications** pour ajouter votre application personnalisée, vous configurez les options dans le fichier du manifeste. Pour en savoir plus sur le fichier manifeste, consultez [Manifeste d’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Pour en savoir plus sur les standards SSO, consultez [Authentification et autorisation avec la plateforme d’identités Microsoft](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> D’autres scénarios dans lesquels **l’authentification unique** sera absente de la navigation incluent les cas où une application est hébergée dans un autre locataire ou si votre compte ne dispose pas des autorisations requises (administrateur général, administrateur d’application Cloud, administrateur d’application ou propriétaire du principal de service). Les autorisations peuvent également être à l’origine d’un scénario dans lequel vous pouvez ouvrir **l’authentification unique**, mais vous ne pourrez pas l’enregistrer. En savoir plus sur les rôles d’administration d’Azure AD voir (https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="configure-link"></a>Configurer le lien

Pour définir le lien d’une application, sélectionnez **Authentification liée** sur la page **Authentification unique**. Entrez ensuite le lien et sélectionnez **Enregistrer**. Vous avez besoin d’un rappel pour trouver ces options ? Consultez la [série de démarrages rapides](view-applications-portal.md).
 
Une fois que vous avez configuré une application, assignez-lui des utilisateurs et des groupes. Lorsque vous affectez des utilisateurs, vous pouvez contrôler le moment où l’application s’affiche dans [Mes applications](https://myapplications.microsoft.com/) ou le lanceur d’applications Microsoft 365.

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md)
- [Configurer le provisionnement automatique d’un compte utilisateur](../app-provisioning/configure-automatic-user-provisioning-portal.md)
