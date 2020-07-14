---
title: Comparer des identités externes – Azure Active Directory | Microsoft Docs
description: Les identités externes Azure AD permettent à des personnes extérieures à votre organisation d’accéder à vos applications et à vos ressources en utilisant leur propre identité. Comparez les solutions pour les identités externes, à savoir Azure AD B2B Collaboration et Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c45aae35c7c1cf2fbde436303a2ac000dfdd5ec
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339606"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>Comparer les solutions pour les identités externes dans Azure Active Directory

Avec les identités externes dans Azure AD, vous pouvez autoriser des personnes extérieures à votre organisation à accéder à vos applications et ressources, tout en leur permettant de se connecter à l’aide de l’identité de leur choix. Vos partenaires, distributeurs, fournisseurs, distributeurs et autres utilisateurs invités peuvent « apporter leurs propres identités ». Qu’ils fassent partie d’Azure AD ou d’un autre système managé par le service informatique, ou qu’ils disposent d’une identité sociale non managée telle que Google ou Facebook, ils peuvent utiliser leurs propres informations d’identification pour se connecter. Le fournisseur d’identité gère l’identité de l’utilisateur externe et vous gérez l’accès à vos applications avec Azure AD pour protéger vos ressources. 

## <a name="external-identities-scenarios"></a>Scénarios d’identités externes

Les identités externes Azure AD se concentrent moins sur la relation d’un utilisateur avec votre organisation, et plus sur la façon dont un individu souhaite se connecter à vos applications et ressources. Dans ce cadre, Azure AD prend en charge un vaste éventail de scénarios, de la collaboration interentreprises (B2B) au développement d’applications pour des clients et des consommateurs (B2C).

- **Partager des applications avec des utilisateurs externes (B2B Collaboration)** . Invitez des utilisateurs externes dans votre propre locataire en tant qu’utilisateurs « invités » auxquels vous pouvez affecter des autorisations tout en leur permettant d’utiliser leurs informations d’identification existantes (pour l’authentification). Les utilisateurs se connectent aux ressources partagées à l’aide d’un processus simple d’invitation et d’acceptation d’invitation, en utilisant leur compte professionnel ou scolaire, ou n’importe quel compte e-mail. Désormais, avec la disponibilité de flux d’utilisateurs s’inscrivant en libre-service (préversion), vous pouvez également fournir une expérience de connexion pour vos utilisateurs externes via l’application que vous souhaitez partager. Vous pouvez configurer des paramètres de flux utilisateur pour contrôler la façon dont l’utilisateur s’inscrit à l’application, et lui permettre d’utiliser son compte professionnel, son compte scolaire ou toute identité sociale (telle que Google ou Facebook) qu’il souhaite utiliser.  Pour plus d’informations, consultez la [documentation Azure AD B2B](index.yml).

- **Développez des applications destinées à d’autres locataires Azure AD (à locataire unique ou mutualisées)** . Lorsque vous développez des applications pour Azure AD, vous pouvez cibler des utilisateurs d’une seule organisation (locataire unique) ou des utilisateurs de toute organisation qui possède déjà un locataire Azure AD (appelées applications mutualisées). Ces applications mutualisées sont inscrites une fois par vous-même dans votre propre Azure AD, mais elles peuvent être utilisées par n’importe quel utilisateur Azure AD à partir de n’importe quelle organisation sans travail supplémentaire de votre part.

- **Développez des applications en marque blanche pour des consommateurs et des clients (Azure AD B2C)** . Si vous êtes une entreprise ou un développeur qui crée des applications accessibles aux clients, vous pouvez les mettre à l’échelle des consommateurs, clients ou citoyens à l’aide d’un Azure AD B2C. Les développeurs peuvent utiliser Azure AD comme système d’identité complet pour leur application, tout en permettant aux clients de se connecter avec une identité qu’ils utilisent déjà (telle que Facebook ou Gmail). Avec Azure AD B2C, vous pouvez complètement personnaliser et contrôler la façon dont les clients s’inscrivent, se connectent et gèrent leurs profils lorsqu’ils utilisent vos applications. Pour plus d’informations, consultez la [documentation Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/).

Le tableau ci-dessous fournit une comparaison détaillée des différents scénarios que vous pouvez activer avec des identités externes Azure AD.

| Applications multilocataires  | Collaboration d’utilisateur externe (B2B) | Applications pour consommateurs ou clients (B2C)  |
| ---- | --- | --- |
| Scénario principal : logiciel en tant que service (SaaS) d’entreprise | Scénario principal : Collaboration à l’aide d’applications Microsoft (Microsoft 365, Teams, …) ou de votre propre logiciel de collaboration.  | Scénario principal : applications transactionnelles utilisant des applications développées personnalisées.   |
| Usage prévu : organisations qui souhaitent fournir des logiciels à de nombreux clients d’entreprise.    | Usage prévu : les entreprises qui souhaitent être en mesure d’authentifier les utilisateurs issus d’une organisation partenaire, quel que soit le fournisseur d’identité.    | Usage prévu : invitation de clients de vos applications mobiles et web, qu’il s’agisse d’individus, de clients institutionnel ou organisationnel dans un répertoire Azure AD séparé de l’annuaire de votre organisation. |
| Identités prises en charge : employés avec des comptes Azure AD. | Identités prises en charge : employés ou partenaires disposant d’un compte professionnel ou scolaire, ou de n’importe quelle adresse e-mail. La fédération directe sera bientôt prise en charge.      | Identités prises en charge : utilisateurs consommateurs disposant de comptes d’application locaux (n’importe quel nom d’utilisateur ou adresse e-mail) ou toute identité sociale prenant en charge la fédération directe.       |
| Les utilisateurs externes sont gérés dans leur propre répertoire, isolé du répertoire dans lequel l’application a été inscrite.    | Les utilisateurs externes sont gérés dans le même annuaire que les employés, mais font l’objet d’une annotation spéciale. Ils peuvent être gérés de la même façon que des employés, être ajoutés aux mêmes groupes, etc.    | Les utilisateurs externes sont gérés dans l’annuaire de l’application. Ils sont gérés séparément du répertoire des partenaires et des employés de l’organisation (s’il existe).  |
| Authentification unique : l’authentification unique (SSO) auprès de toutes les applications connectées à Azure AD est prise en charge.          | Authentification unique : l’authentification unique (SSO) auprès de toutes les applications connectées à Azure AD est prise en charge. Par exemple, vous pouvez donner accès à des applications Microsoft 365 ou des applications locales, et à d’autres applications SaaS telles que Salesforce ou Workday.    | Authentification unique : L’authentification unique auprès d’applications détenues par le client dans les clients Azure AD B2C est prise en charge. L’authentification unique auprès de Microsoft 365 ou d’autres applications SaaS Microsoft n’est pas prise en charge.    |
| Cycle de vie client : géré par l’organisation d’origine de l’utilisateur.      | Cycle de vie partenaire : géré par l’organisation hôte (qui invite).    | Cycle de vie client : libre-service ou géré par l’application.      |
| Stratégie de sécurité et conformité : gérée par l’organisation hôte ou qui invite (par exemple, avec des [règles d’accès conditionnelles](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).           | Stratégie de sécurité et conformité : gérée par l’organisation hôte ou qui invite (par exemple, avec des [règles d’accès conditionnelles](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)). | Stratégie de sécurité et conformité : gérée par l’application.        |
| Marque : la marque de l’organisation hôte (qui invite) est utilisée.   | Marque : la marque de l’organisation hôte (qui invite) est utilisée.    | Marque : gérée par l’application. En général, la marque du produit apparaît, avec l’organisation en arrière-plan.   |
| Informations supplémentaires : [Gérer l’identité dans des applications mutualisées](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [Guide pratique](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Informations supplémentaires : [Billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentation](what-is-b2b.md)                   | Informations supplémentaires : [Page produit](https://azure.microsoft.com/services/active-directory-b2c/), [Documentation](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Sécurisez et gérez des clients et partenaires au-delà des limites de votre organisation avec des identités externes Azure AD.

### <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [À propos d’Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
