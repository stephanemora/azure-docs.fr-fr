---
title: Ajouter une authentification à vos applications mobiles avec Visual Studio App Center et les services Azure
description: Découvrez les services comme Visual Studio App Center qui aident à configurer l’authentification des utilisateurs et permettent aux applications mobiles de s’authentifier avec des comptes de réseaux sociaux, Azure Active Directory et une authentification personnalisée.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: a20ec1039dc8fc477410891495762395b408f2d6
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870573"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Ajouter une authentification et gérer les identités des utilisateurs dans vos applications mobiles

Le fait de voir l’utilisateur et son comportement dans votre application permet aux développeurs de fidéliser les utilisateurs en leur créant des expériences sur-mesure. Que vous soyez un développeur d’applications créant une application de collaboration pour des utilisateurs dans votre organisation ou que vous créiez la future plateforme de réseaux sociaux, vous avez besoin d’un moyen d’authentifier les utilisateurs et de gérer leurs identités. Le service de gestion des identités est l’une des fonctionnalités les plus importantes d’un service back-end mobile.

Utilisez les services suivants pour activer l’authentification des utilisateurs dans vos applications mobiles.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) est un service de gestion des identités entreprise-client (B2C) que les développeurs peuvent utiliser afin d’authentifier leurs clients. Ce service de marque blanche permet aux développeurs de personnaliser et de contrôler la façon dont les utilisateurs interagissent de façon sécurisée avec vos applications web, de bureau, mobiles ou monopages. Avec Azure AD B2C, les utilisateurs peuvent s’inscrire, se connecter, réinitialiser les mots de passe et modifier des profils. Azure AD B2C implémente une forme des protocoles OpenID Connect et OAuth 2.0. 

**Fonctionnalités clés**
- Authentifiez de façon sécurisée les clients à l’aide de leur fournisseur d’identité préféré.
- Gérez les identités et les accès des clients.
- Bénéficiez d’une prise en charge des connexions pour les réseaux sociaux tels que Facebook, GitHub, Google, LinkedIn, Twitter, WeChat et Weibo.
- Connectez-vous à vos comptes d’utilisateur à l’aide des protocoles standard du secteur, comme OpenID Connect ou SAML, pour rendre la gestion des identités possible sur différentes plateformes.
- Offrez des expériences d’inscription et de connexion personnalisées.
- Bénéficiez d’une intégration facile aux bases de données CRM, outils de marketing analytique et systèmes de vérification de compte.
- Capturez des données de connexion, de préférence et de conversion pour les clients.

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Documentation d’Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Démarrages rapides](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Exemples](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service de gestion des identités et des accès basé sur le cloud de Microsoft. Il permet à vos employés de se connecter et d’accéder aux ressources suivantes :
- Ressources externes telles que Microsoft Office 365, le Portail Azure et des milliers d’autres applications SaaS (Software as a service).
- Ressources internes telles que les applications situées sur votre réseau d’entreprise et intranet ainsi que les applications cloud développées par votre propre organisation.

**Fonctionnalités clés**
- Accès fluide et hautement sécurisé en connectant les utilisateurs aux applications dont ils ont besoin.
- Protection complète des identités et sécurité renforcée des identités et des accès en fonction de l’utilisateur, de l’emplacement, de l’appareil, des données et du contexte d’application.
- Milliers d’applications pré-intégrées à la fois commerciales et personnalisées comme Office 365, Salesforce.com et Box.
- Capacité à gérer des accès à grande échelle.

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Présentation d’Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Prise en main d’Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Démarrages rapides](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)