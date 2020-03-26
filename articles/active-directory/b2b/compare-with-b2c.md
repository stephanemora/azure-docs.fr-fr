---
title: Comparer B2B Collaboration et B2C - Azure Active Directory | Microsoft Docs
description: Quelle est la différence entre Azure Active Directory B2B Collaboration et Azure AD B2C ?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "68380737"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparer B2B Collaboration et B2C dans Azure Active Directory

Azure Active Directory (Azure AD) B2B Collection et Azure AD B2C vous permettent de travailler avec des utilisateurs externes dans Azure AD. En quoi se différencient-ils ?

**Azure AD B2B** convient aux entreprises qui souhaitent partager des fichiers et des ressources de manière sécurisée afin de collaborer avec des utilisateurs externes. Un administrateur Azure configure le B2B dans le portail Azure, et Azure AD se charge de la fédération entre votre entreprise et vos partenaires externes. Les utilisateurs se connectent aux ressources partagées à l’aide d’un processus simple d’invitation et d’échange d’invitation, en utilisant leur compte professionnel ou scolaire, ou n’importe quel autre compte e-mail.
 
**Azure AD B2C** s’adresse principalement aux entreprises et aux développeurs qui créent des applications destinées aux clients. Avec Azure AD B2C, les développeurs peuvent utiliser Azure AD comme un système d’identité complet pour leur application, tout en permettant aux clients de se connecter avec une identité qu’ils utilisent déjà (telle que Facebook ou Gmail).

Le tableau ci-dessous donne une comparaison détaillée.


Fonctionnalités de B2B Collaboration |     Offre autonome d’Azure AD B2C
-------- | --------
Usage prévu : les entreprises qui souhaitent être en mesure d’authentifier les utilisateurs issus d’une organisation partenaire, quel que soit le fournisseur d’identité. | Usage prévu : inviter les clients de vos applications mobiles et web (qu’il s’agisse d’individus, d’institutions ou d’organisations) dans votre Azure AD.
Identités prises en charge : employés ou partenaires disposant d’un compte professionnel ou scolaire, ou de n’importe quelle adresse e-mail. La fédération directe sera bientôt prise en charge.  | Identités prises en charge : utilisateurs consommateurs disposant de comptes d’application locaux (n’importe quel nom d’utilisateur ou adresse e-mail) ou toute identité sociale prenant en charge la fédération directe.
Les utilisateurs externes sont gérés dans le même annuaire que les employés, mais font l’objet d’une annotation spéciale. Ils peuvent être gérés de la même façon que les employés, être ajoutés aux mêmes groupes, etc.  | Les utilisateurs externes sont gérés dans l’annuaire de l’application. Ils sont gérés séparément de l’annuaire des partenaires et des employés de l’organisation (s’il existe).
L’authentification unique (SSO) sur toutes les applications connectées à Azure AD est prise en charge. Par exemple, vous pouvez donner accès à des applications Office 365 ou des applications locales, et à d’autres applications SaaS telles que Salesforce ou Workday.  |  L’authentification unique auprès d’applications détenues par le client dans les clients Azure AD B2C est prise en charge. L’authentification unique auprès d’Office 365 ou d’autres applications SaaS Microsoft n’est pas prise en charge.
Cycle de vie partenaire : géré par l’organisation hôte (qui invite).  | Cycle de vie client : libre-service ou géré par l’application.
Stratégie de sécurité et conformité : gérée par l’organisation hôte ou qui invite (par exemple, avec des [règles d’accès conditionnelles](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Stratégie de sécurité et conformité : gérée par l’application.
Marque : la marque de l’organisation hôte (qui invite) est utilisée.  |    Marque : gérée par l’application. En général, la marque du produit apparaît, avec l’organisation en arrière-plan.
Informations supplémentaires : [Billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentation](what-is-b2b.md)  | Informations supplémentaires : [Page produit](https://azure.microsoft.com/services/active-directory-b2c/), [Documentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Propriétés de l’utilisateur B2B Collaboration](user-properties.md)

