---
title: Rôles et contrôle d’accès aux ressources
titleSuffix: Azure AD B2C
description: Découvrez comment utiliser des rôles pour contrôler l’accès aux ressources.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 672df647bb655210ab759278a69e63a38abc1cf4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035293"
---
# <a name="roles-and-resource-access-control"></a>Rôles et contrôle d’accès aux ressources

Lors de la planification de votre stratégie de contrôle d’accès, il est préférable d’attribuer le rôle le moins privilégié requis pour accéder aux ressources. Le tableau suivant décrit les ressources principales de votre locataire Azure AD B2C et les rôles administratifs les plus appropriés pour les utilisateurs qui les gèrent.

|Ressource  |Description  |Role  |
|---------|---------|---------|
|[Inscriptions d’applications](tutorial-register-applications.md) | Créez et gérez tous les aspects de vos inscriptions d’applications web, mobiles et natives dans Azure AD B2C.|[Administrateur d’application](../active-directory/roles/permissions-reference.md#application-administrator)|
|[Fournisseurs d’identité](add-identity-provider.md)| Configurez le [fournisseur d’identité local](identity-provider-local.md) et les fournisseurs d’identité sociale ou d’entreprise externes. | [Administrateur de fournisseurs d’identité externes](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Connecteurs d’API](add-api-connector.md)| Intégrez vos flux d’utilisateurs aux API web pour personnaliser l’expérience utilisateur et pour intégrer des systèmes externes.|[Administrateur de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Marque de société](customize-ui.md#configure-company-branding)| Personnalisez vos pages de flux d’utilisateurs.| [Administrateur général](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Attributs utilisateur](user-flow-custom-attributes.md)| Ajoutez ou supprimez des attributs personnalisés disponibles pour tous les flux d’utilisateurs.| [Administrateur d’attribut de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Gestion des utilisateurs| Gérez les [comptes consommateur](manage-users-portal.md) et les comptes d’administration comme décrit dans cet article.| [Administrateur d’utilisateurs](../active-directory/roles/permissions-reference.md#user-administrator)|
|Rôles et administrateurs| Gérer les attributions de rôle dans le répertoire Azure AD B2C. Créez et gérez des groupes qui peuvent être affectés à des rôles Azure AD B2C. |[Administrateur général](../active-directory/roles/permissions-reference.md#global-administrator), [Administrateur de rôle privilégié](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Flux d’utilisateurs](user-flow-overview.md)|Pour une configuration rapide et l’activation de tâches d’identité courantes, telles que l’inscription, la connexion et la modification de profil.| [Administrateur de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Stratégies personnalisées](user-flow-overview.md)| Créez, lisez, mettez à jour et supprimez toutes les stratégies personnalisées dans Azure AD B2C.| [Administrateur de stratégies B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Clés de stratégies](policy-keys-overview.md)|Ajoutez et gérez des clés de chiffrement pour la signature et la validation de jetons, de clés secrètes client, de certificats et de mots de passe utilisés dans des stratégies personnalisées.|[Administrateur de jeux de clés B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|