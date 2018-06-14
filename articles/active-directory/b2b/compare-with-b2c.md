---
title: Comparer B2B Collaboration et B2C dans Azure Active Directory | Microsoft Docs
description: Quelle est la différence entre Azure Active Directory B2B Collaboration et Azure AD B2C ?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 19e8e9d22938fa8a18299d67aa77824aaae3f6da
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267000"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparer B2B Collaboration et B2C dans Azure Active Directory

Azure Active Directory (Azure AD) B2B Collection et Azure AD B2C vous permettent de travailler avec des utilisateurs externes dans Azure AD. En quoi se différencient-ils ?


Fonctionnalités de B2B Collaboration |     Offre autonome d’Azure AD B2C
-------- | --------
Public visé : les entreprises qui souhaitent être en mesure d’authentifier les utilisateurs à partir d’une organisation partenaire, quel que soit le fournisseur d’identité. | Public visé : inviter les clients de vos appareils mobiles et applications web, qu’il s’agisse d’individus, d’institutions ou d’organisations dans votre Azure AD.
Identités prises en charge : employés ou partenaires disposant d’un compte professionnel ou scolaire ou n’importe quelle adresse de messagerie. La fédération directe sera bientôt prise en charge.  | Identités prises en charge : utilisateurs consommateurs disposant de comptes d’application locaux (n’importe quel nom d’utilisateur ou adresse de messagerie) ou toute identité sociale prenant en charge la fédération directe.
Répertoire dans lequel les utilisateurs partenaire se trouvent : les utilisateurs partenaire de l’organisation externe sont gérés dans le même répertoire que les employés, mais font l’objet d’une annotation spéciale. Ils peuvent être gérés de la même façon que les employés, ils peuvent être ajoutés aux mêmes groupes, etc.  | Répertoire dans lequel les entités d’utilisateur client se trouvent : dans le répertoire de l’application. Gestion indépendamment du répertoire de partenaires et d’employés de l’entreprise (le cas échéant).
L’authentification unique (SSO) sur toutes les applications connectées à Azure AD est prise en charge. Par exemple, vous pouvez donner accès à des applications Office 365 ou des applications locales, et à d’autres applications SaaS telles que Salesforce ou Workday.  |  L’authentification unique auprès d’applications détenues par le client dans les clients Azure AD B2C est prise en charge. L’authentification unique auprès d’Office 365 ou d’autres applications Microsoft ou SaaS non Microsoft n’est pas prise en charge.
Cycle de vie partenaire : géré par l’organisation hôte/qui invite.  | Cycle de vie client : libre-service ou géré par l’application.
Stratégie de sécurité et conformité : gérée par l’organisation hôte/qui invite.  | Stratégie de sécurité et conformité : gérée par l’application.
Marque : la marque de l’organisation hôte/qui invite est utilisée.  |    Marque : gérée par l’application. En général, la marque du produit apparaît, avec l’organisation en arrière-plan.
Plus d’informations : [Billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentation](what-is-b2b.md)  | Plus d’informations : [Page produit](https://azure.microsoft.com/services/active-directory-b2c/), [Documentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Propriétés de l’utilisateur B2B Collaboration](user-properties.md)

