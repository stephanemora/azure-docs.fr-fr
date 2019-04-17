---
title: Recherche de nom d’utilisateur au cours de l’authentification de connexion - Azure Active Directory | Microsoft Docs
description: Comment l’écran messagerie reflète le nom d’utilisateur recherche pendant la connexion
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608773"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Découverte de domaine d’accueil pour les pages de connexion Azure Active Directory

Nous modifions notre comportement de connexion à Azure Active Directory (Azure AD) pour laisser place à de nouvelles méthodes d'authentification et améliorer la convivialité. Lors de la connexion, Azure AD détermine où un utilisateur doit s'authentifier. Azure AD prend des décisions intelligentes en lisant les paramètres d'organisation et d'utilisateur qui s'appliquent au nom d'utilisateur entré sur la page de connexion. Il s'agit d'un pas en avant vers un avenir sans mot de passe avec la possibilité d'utiliser des informations d'identification supplémentaires telles que FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportement de découverte de domaine d’accueil

Historiquement, la découverte du domaine d’accueil a été régie par le domaine qui est fourni à la connexion ou par une stratégie de découverte du domaine domestique pour certaines applications héritées. Par exemple, dans notre comportement de découverte un utilisateur Azure Active Directory impossible faute de frappe dans leur nom d’utilisateur, mais toujours arrivent sur l’écran de collection d’informations d’identification de leur organisation. Cela se produit lorsque l’utilisateur répond correctement le nom de domaine de « contoso.com » l’organisation. Ce comportement n'offre pas la granularité nécessaire pour personnaliser l'expérience d'un utilisateur individuel.

Pour prendre en charge un large éventail d’informations d’identification et de faciliter l’utilisation, le comportement de recherche de nom d’utilisateur Azure Active Directory pendant le processus de connexion est désormais mis à jour. Le nouveau comportement prend des décisions intelligentes en lisant les paramètres de niveau client et utilisateur basés sur le nom d’utilisateur entré sur la page de connexion. Pour ce faire, Azure Active Directory vérifie si le nom d’utilisateur est entré dans la page de connexion existe dans leur domaine spécifié ou qu’il redirige l’utilisateur pour fournir leurs informations d’identification.

Un autre avantage de ce travail est l’amélioration des erreurs de messagerie. Voici quelques exemples de l’erreur améliorée messagerie lorsque vous vous connectez à une application qui prend en charge uniquement les utilisateurs Azure Active Directory.

1. Le nom d’utilisateur est mal orthographiée ou le nom d’utilisateur n’a pas encore été synchronisé à Azure AD :
  
    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/signin-realm-discovery/typo-username.png)
  
2. Le nom de domaine est mal tapé :
  
    ![le nom de domaine est mal orthographiée ou introuvable](./media/signin-realm-discovery/typo-domain.png)
  
3. Utilisateur tente de se connecter avec un domaine consommateur connus :
  
    ![Connectez-vous à l’aide d’un domaine consommateur connus](./media/signin-realm-discovery/consumer-domain.png)
  
4. Le mot de passe mal orthographié, mais le nom d’utilisateur est précis :  
  
    ![faute de frappe de mot de passe avec le bon nom d’utilisateur](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Cette fonctionnalité peut avoir un impact sur les domaines fédérés ancien domaine au niveau de la découverte d’accueil pour forcer la fédération de partie de confiance. Pour les mises à jour sur la prise en charge du domaine fédéré lorsque sera ajouté, consultez [découverte du domaine d’accueil lors de la connexion pour les services Microsoft 365](https://azure.microsoft.com/en-us/updates/signin-hrd/). En attendant, certaines organisations ont formé leurs employés de se connecter avec un nom d’utilisateur qui n’existe pas dans Azure Active Directory mais qui contient le nom de domaine approprié, étant donné que les noms de domaine achemine les utilisateurs actuellement au point de terminaison de domaine de leur organisation. Le nouveau comportement de connexion ne permet pas cela. L’utilisateur est prévenu pour corriger le nom d’utilisateur, et ils ne sont pas autorisés à se connecter avec un nom d’utilisateur qui n’existe pas dans Azure Active Directory.
>
> Si vous ou votre organisation avez pratiques qui dépendent de l’ancien comportement, il est important que les administrateurs d’entreprise pour mettre à jour de la documentation de l’authentification et l’authentification par employé et pour l’apprentissage aux employés d’utiliser leur nom d’utilisateur Azure Active Directory pour vous connecter.
  
Si vous avez des problèmes avec le nouveau comportement, laissez vos remarques dans le **commentaires** section de cet article.  

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser votre connexion de personnalisation](../fundamentals/add-custom-domain.md)
