---
title: Partage de données d'intégration LinkedIn et consentement - Azure Active Directory | Microsoft Docs
description: Explique comment l'intégration LinkedIn partage les données par le biais des applications Microsoft dans Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71eeed62c6de2a3771909e8489ca816930993435
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442220"
---
# <a name="linkedin-integration-data-sharing-and-consent"></a>Partage de données et consentement d’intégration LinkedIn

En tant qu’administrateur Azure Active Directory (Azure AD), vous pouvez autoriser les utilisateurs de votre organisation à donner leur consentement pour se connecter à leur compte professionnel ou scolaire Microsoft avec leur compte LinkedIn. Lorsque les utilisateurs se connectent à leur compte, les informations et les éléments clés de LinkedIn sont disponibles dans certaines applications et certains services Microsoft. Les utilisateurs peuvent également s’attendre à une amélioration et un enrichissement de leur expérience de réseau sur LinkedIn avec les informations issues de Microsoft.

Pour afficher des informations LinkedIn dans les applications et services Microsoft, les utilisateurs doivent donner leur consentement afin de se connecter à leurs propres comptes Microsoft et LinkedIn. Les utilisateurs sont invités à se connecter à leur compte la première fois qu’ils cliquent pour afficher les informations LinkedIn d’une personne sur une carte de visite Outlook, OneDrive ou SharePoint Online. Les connexions de compte LinkedIn ne sont pas entièrement activées pour vos utilisateurs jusqu’à ce qu’ils aient donné leur consentement pour l’expérience et la connexion à leur compte.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Avantages du partage des informations LinkedIn

L’accès aux informations LinkedIn dans les applications et services Microsoft facilite la connexion, l’implication et l’instauration de relations professionnelles de vos utilisateurs avec des collègues, clients et partenaires au sein et en dehors de votre organisation. Les nouveaux utilisateurs peuvent être opérationnels plus rapidement en se connectant avec des collègues, en ayant plus d’informations à leur sujet et en accédant facilement à plus de données. Voici un exemple d’affichage d’informations LinkedIn sur la carte de visite dans les applications Microsoft :

![Activation de l’intégration LinkedIn](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Activer et annoncer l’intégration LinkedIn

Vous devez être administrateur Azure Active Directory pour gérer le paramètre de votre organisation. Vous pouvez l’activer pour tous les utilisateurs ou pour un ensemble spécifique d’utilisateurs.

1. Pour activer ou désactiver l’intégration, suivez la procédure décrite dans [Intégration LinkedIn](linkedin-integration.md).
2. Lorsque vous annoncez l’intégration LinkedIn dans votre organisation, orientez vos utilisateurs vers la FAQ sur les [informations LinkedIn dans les applications et services Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). L’article fournit des informations sur l’emplacement des informations LinkedIn, le mode de connexion des comptes, etc.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentement de l’utilisateur pour accéder aux données dans Microsoft et LinkedIn

Les données accessibles à partir de LinkedIn ne sont pas stockées définitivement dans les services Microsoft. Les données accessibles à partir de Microsoft ne sont pas stockées définitivement avec LinkedIn, à l’exception des Contacts. Les Contacts Microsoft sont stockés sur LinkedIn jusqu’à ce que les utilisateurs les suppriment, comme décrit dans [deleting imported contacts from LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377) (Suppression des contacts importés à partir de LinkedIn).

Lorsque les utilisateurs se connectent à leur compte, les informations et les aperçus de LinkedIn sont disponibles dans certaines applications Microsoft, comme la carte de visite. Les utilisateurs peuvent également s’attendre à une amélioration et un enrichissement de leur expérience de réseau sur LinkedIn avec les informations issues de Microsoft.
Quand des utilisateurs de votre organisation se connectent à leurs comptes LinkedIn et Microsoft scolaire ou professionnel, ils ont deux options :

* Autorisez l’accès aux données à partir de ces deux comptes. Cela signifie qu’ils donnent à leur compte Microsoft ou professionnel l’autorisation d’accéder aux données à partir de leur compte LinkedIn, et à [leur compte LinkedIn l’autorisation d’accéder aux données à partir de leur compte scolaire ou professionnel Microsoft](https://www.linkedin.com/help/linkedin/answer/84077).
* Autorisez uniquement l’accès des données LinkedIn au moyen de leur compte professionnel ou scolaire Microsoft.

Les utilisateurs peuvent se déconnecter de leur compte et supprimer les autorisations d’accès aux données à tout moment. [Ils peuvent aussi contrôler la manière dont leur profil LinkedIn s’affiche](https://www.linkedin.com/help/linkedin/answer/83), et notamment choisir si leur profil peut apparaître dans les applications Microsoft.

### <a name="linkedin-account-data"></a>Données de compte LinkedIn

Lorsque vous connectez vos comptes Microsoft et LinkedIn, vous autorisez LinkedIn à fournir les données suivantes à Microsoft :

* Données de profil : cela inclut l’identité LinkedIn, les informations de contact et les informations que vous partagez avec d’autres utilisateurs sur votre [profil LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Données d’intérêt : cela inclut les centres d’intérêt sur LinkedIn, comme les personnes et les sujets vous suivez, les groupes de formation, et le contenu que vous aimez et partagez.
* Données d’abonnement : cela inclut les abonnements aux applications et services LinkedIn, ainsi que les données associées. 
* Données de connexion : cela inclut votre [réseau LinkedIn](https://www.linkedin.com/help/linkedin/answer/110), y compris les profils et les informations de contact de vos connexions au 1er degré.

Les données accessibles à partir de LinkedIn ne sont pas stockées définitivement dans les services Microsoft. Pour plus d’informations sur l’utilisation des données personnelles par Microsoft, consultez la [Déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Données de compte professionnel ou scolaire Microsoft

Lorsque vous connectez vos comptes Microsoft et LinkedIn, vous autorisez Microsoft à fournir les données suivantes à LinkedIn :

* Données de profil : cela inclut des informations telles que votre prénom, votre nom de famille, votre photo de profil, votre adresse e-mail, votre responsable et les personnes dont vous êtes responsable.
* Données de calendrier : cela inclut les réunions inscrites dans vos calendriers, leurs horaires, leurs lieux et les informations de contact des participants. Les informations sur la réunion, comme l’ordre du jour, le contenu ou le titre de la réunion, ne sont pas incluses dans les données de calendrier.
* Données d’intérêt : cela inclut les centres d’intérêt associés à votre compte, en fonction de votre utilisation des services Microsoft, comme Cortana et Bing for Business.
* Données d’abonnement : cela inclut les abonnements fournis par votre organisation aux applications et services Microsoft, comme Office 365.
* Données de contact : cela inclut les listes de contacts dans Outlook, Skype et d’autres services de compte Microsoft, y compris les informations de contact des personnes avec lesquelles vous communiquez ou collaborez fréquemment. Les contacts seront régulièrement importés, stockés et utilisés par LinkedIn, par exemple pour suggérer des connexions, vous aider à organiser vos contacts et afficher les mises à jour sur les contacts.

Les données accessibles à partir de Microsoft ne sont pas stockées définitivement avec LinkedIn, à l’exception des Contacts. Les Contacts Microsoft sont stockés sur LinkedIn jusqu’à ce que les utilisateurs les suppriment. En savoir plus sur la [suppression des contacts importés à partir de LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Pour plus d’informations sur l’utilisation des données personnelles de LinkedIn, consultez la [politique de confidentialité LinkedIn](https://www.linkedin.com/legal/privacy-policy). Pour les services LinkedIn, transfert de données et stockage, les données peuvent circuler à partir de l’Union européenne vers les États-Unis et inversement, et votre vie privée est protégée, comme décrit dans [les transferts de données de l’Union européenne](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Étapes suivantes

* [LinkedIn dans les applications Microsoft avec votre compte professionnel ou scolaire](https://www.linkedin.com/help/linkedin/answer/84077)
