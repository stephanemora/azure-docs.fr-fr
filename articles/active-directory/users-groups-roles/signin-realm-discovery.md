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
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007570"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Découverte de domaine d’accueil lors de la connexion pour les services Microsoft 365

Nous modifions notre comportement de connexion Azure Active Directory (Azure AD) pour faire place aux nouvelles méthodes d’authentification et d’améliorer la facilité d’utilisation. Pendant la connexion, Azure AD détermine où un utilisateur doit s’authentifier. Azure AD prend des décisions intelligentes en lisant les paramètres de l’organisation et utilisateur pour le nom d’utilisateur entré sur la page de connexion. Il s’agit d’une étape vers une future sans mot de passe qui permet des informations d’identification supplémentaires comme FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportement de découverte de domaine d’accueil

Historiquement, la découverte du domaine d’accueil a été régie par le domaine qui est fourni à la connexion ou par une stratégie de découverte du domaine domestique pour certaines applications héritées. Par exemple, dans l’ancien comportement de découverte un utilisateur Azure Active Directory impossible faute de frappe dans leur nom d’utilisateur, mais toujours arrivent sur l’écran de collection d’informations d’identification de leur organisation. Cela se produit lorsque l’utilisateur répond correctement le nom de domaine de « contoso.com » l’organisation. Ce comportement n’autorise pas la granularité personnaliser les expériences pour un utilisateur individuel.

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
  
## <a name="additional-info"></a>Informations supplémentaires

Outre l’expérience utilisateur de connexion améliorée, cette modification inclut des mécanismes qui peuvent aider à atténuer l’abus d’énumération de nom d’utilisateur à grande échelle.

Cette modification est initialement ciblée pour les domaines managés et commence le déploiement dans mai 2019, mais ne démarre pas déployer sur des domaines fédérés à la fin de 2019. Les dates exactes de déploiement pour les domaines fédérés varie en fonction des commentaires des clients.

> [!IMPORTANT]
> Cette fonctionnalité aura un impact significatif sur les domaines fédérés ancien domaine au niveau de la découverte d’accueil pour forcer la fédération de partie de confiance. Certaines organisations ont formé à leurs employés de se connecter avec un nom d’utilisateur qui n’existe pas dans Azure Active Directory mais qui contient le nom de domaine approprié, étant donné que les noms de domaine achemine les utilisateurs actuellement au point de terminaison de domaine de leur organisation. Le nouveau comportement de connexion ne permet pas cela. L’utilisateur est prévenu pour corriger le nom d’utilisateur, et ils ne sont pas autorisés à se connecter avec un nom d’utilisateur qui n’existe pas dans Azure Active Directory.
>
> Si vous ou votre organisation avez pratiques qui dépendent de l’ancien comportement, il est important que les administrateurs d’entreprise pour mettre à jour de la documentation de l’authentification et l’authentification par employé et pour l’apprentissage aux employés d’utiliser leur nom d’utilisateur Azure Active Directory pour vous connecter.
  
Si vous avez des problèmes avec le nouveau comportement, veuillez laisser vos remarques dans le **commentaires** section de cet article.  

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser votre connexion de personnalisation](../fundamentals/add-custom-domain.md)