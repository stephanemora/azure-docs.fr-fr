---
title: Accès conditionnel - Exiger l’authentification multifacteur pour les administrateurs - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour demander aux administrateurs d’effectuer l’authentification multifacteur
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45263ca0448042aa972ee53093b51dd47bd51190
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049347"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Accès conditionnel : Exiger MFA pour les administrateurs

Les comptes auxquels sont affectés les droits d’administration sont ciblés par les attaquants. Exiger l’authentification multifacteur (MFA) sur ces comptes est un moyen simple de réduire le risque de compromission de ces comptes.

Microsoft vous recommande d’exiger une authentification MFA sur les rôles suivants au minimum :

* Administrateur de facturation
* Administrateur de l’accès conditionnel
* Administrateur Exchange
* Administrateur général
* Administrateur du support (mot de passe)
* Administrateur de mots de passe
* Administrateur de sécurité
* Administrateur SharePoint
* Administrateur d’utilisateurs

Les organisations peuvent choisir d’inclure ou d’exclure des rôles comme ils le jugent adéquat.

## <a name="user-exclusions"></a>Exclusions d’utilisateurs

Les stratégies d’accès conditionnel sont des outils puissants. Nous vous recommandons donc d’exclure les comptes suivants de votre stratégie :

* Comptes **d’accès d’urgence** ou **de secours** pour empêcher le verrouillage du compte sur l’ensemble du locataire. Dans le scénario improbable où tous les administrateurs seraient verrouillés hors de votre locataire, votre compte administratif d’accès d’urgence peut être utilisé pour vous connecter au locataire et prendre les mesures nécessaires pour récupérer l’accès.
   * Pour plus d’informations, consultez l’article [Gérer des comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md).
* Les **comptes de service** et les **principaux de service**, comme le compte de synchronisation Azure AD Connect. Les comptes de service sont des comptes non interactifs qui ne sont pas liés à un utilisateur particulier. Ils sont généralement utilisés par les services principaux autorisant l’accès par programme aux applications, mais ils sont également utilisés pour se connecter aux systèmes à des fins administratives. Les comptes de service comme ceux-ci doivent être exclus, car l’authentification multifacteur ne peut pas être effectuée par programme. Les appels effectués par les principaux de service ne sont pas bloqués par l’accès conditionnel.
   * Si votre organisation utilise ces comptes dans des scripts ou du code, envisagez de les remplacer par des [identités managées](../managed-identities-azure-resources/overview.md). Pour contourner provisoirement le problème, vous pouvez exclure ces comptes spécifiques de la stratégie de base.

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Les étapes suivantes vous aideront à créer une stratégie d’accès conditionnel pour demander à ces rôles d’administration affectés d’effectuer l’authentification multifacteur.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Rôles d’annuaire (préversion)** et choisissez au minimum les rôles suivants :
      * Administrateur d’authentification
      * Administrateur de facturation
      * Administrateur de l’accès conditionnel
      * Administrateur Exchange
      * Administrateur général
      * Administrateur du support technique
      * Administrateur de mots de passe
      * Administrateur de sécurité
      * Administrateur SharePoint
      * Administrateur d’utilisateurs
   
      > [!WARNING]
      > Les stratégies d’accès conditionnel ne prennent pas en charge les utilisateurs affectés à un rôle d’annuaire [étendue à une unité administrative](../users-groups-roles/roles-admin-units-assign-roles.md) ou à des rôles d’annuaire étendus directement à un objet, par exemple via [des rôles personnalisés](../users-groups-roles/roles-create-custom.md).

   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Toutes les applications cloud** et sélectionnez **Terminé**.
1. Sous **Conditions** > **Applications clientes (préversion)** > **Sélectionnez les applications clientes auxquelles cette stratégie s’applique**, laissez toutes les valeurs par défaut sélectionnées et sélectionnez **Terminé**.
1. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Accorder l'accès**, **Requérir l’authentification multifacteur**, et sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Déterminer l'impact à l'aide du mode Rapport seul de l'Accès conditionnel](howto-conditional-access-insights-reporting.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
