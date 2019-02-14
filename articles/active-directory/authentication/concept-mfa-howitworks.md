---
title: 'Azure Multi-Factor Authentication : fonctionnement'
description: Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dabb47d4c4f39d30d8e19e8d8f41b5b84c57fd42
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217596"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Fonctionnement : Azure Multi-Factor Authentication

La sécurité de la vérification en deux étapes repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l’utilisateur, celui-ci lui est inutile sans posséder la méthode d’authentification supplémentaire. Le principe fonctionne sur l’imposition au minimum de deux des méthodes d’authentification suivantes :

* Un élément que vous connaissez (généralement un mot de passe)
* Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
* Un élément vous concernant particulièrement (biométrie)

<center>![Image conceptuelle des méthodes d’authentification](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) participe à la sécurisation de l’accès aux données et aux applications tout en maintenant une simplicité de gestion pour les utilisateurs. MFA fournit une sécurité supplémentaire en exigeant une deuxième forme d’authentification, et procure une authentification renforcée par le biais d’un éventail de [méthodes d’authentification](concept-authentication-methods.md) faciles à utiliser. Les utilisateurs peuvent devoir s'authentifier via MFA selon les choix de configuration de l'administrateur.

## <a name="how-to-get-multi-factor-authentication"></a>Comment obtenir Multi-Factor Authentication ?

Le service Multi-Factor Authentication est fourni avec les offres suivantes :

* **Licences Azure Active Directory Premium** - Utilisation complète du service Azure Multi-Factor Authentication (Cloud) ou du serveur Azure multi-Factor Authentication (Local).
   * **Service Azure MFA (Cloud)** - **Cette option est la voie royale pour les nouveaux déploiements**. Azure MFA dans le cloud ne nécessite aucune infrastructure locale et peut être utilisé avec les utilisateurs fédérés, ou ceux du cloud uniquement.
   * **Serveur Azure MFA** - Si votre organisation souhaite gérer les éléments d’infrastructure associés, et qu’elle a déployé AD FS dans votre environnement local, ce moyen peut constituer une solution.
* **Multi-Factor Authentication pour Office 365** - Une sélection de fonctionnalités Azure Multi-Factor Authentication disponibles dans le cadre de votre abonnement. Apprenez-en davantage sur MFA pour Office 365 en consultant l’article [Planifier les déploiements de l’authentification multifacteur pour Office 365](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Administrateurs généraux Azure Active Directory** - Une sélection de fonctionnalités Azure Multi-Factor Authentication disponibles comme moyen de protection des comptes d’administrateur général.

> [!NOTE]
> De nouveaux clients ne pourront peut-être plus acheter une authentification multifacteur Azure en tant qu’offre autonome à partir du 1er septembre 2018. L’authentification multifacteur restera une fonctionnalité disponible dans les licences Azure AD Premium.

## <a name="supportability"></a>Prise en charge

Dans la mesure où la plupart des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre organisation informe l’ensemble des utilisateurs sur ce processus. Le fait d’en être averti peut réduire la probabilité que les utilisateurs appellent votre support technique pour des problèmes mineurs liés à l’authentification MFA. Toutefois, pour certains scénarios, il est nécessaire de désactiver provisoirement l’authentification Multifacteur. Suivez les indications suivantes pour comprendre comment gérer ces scénarios :

* Formez le personnel de votre support technique à la gestion de scénarios dans lesquels l’utilisateur ne parvient pas à se connecter, car il n’a pas accès à ses méthodes d’authentification, ou parce que celles-ci ne fonctionnent pas correctement.
   * À l’aide de stratégies d’accès conditionnel pour le service Azure MFA, votre personnel du support technique peut ajouter un utilisateur à un groupe qui est exclu d’une stratégie nécessitant MFA.
   * L’équipe du support technique peut activer un contournement temporaire à usage unique pour les utilisateurs du serveur Azure MFA, afin d’autoriser un utilisateur à s’authentifier sans procéder à la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié.   
* Envisagez d’utiliser des adresses IP approuvées ou des emplacements nommés comme moyen de réduire les invites de vérification en deux étapes. Avec cette fonction, les administrateurs d’un locataire géré ou fédéré peuvent contourner la vérification en deux étapes des utilisateurs qui se connectent depuis un emplacement réseau approuvé, tel que l’intranet de leur organisation.
* Déployez [Azure AD Identity Protection](../active-directory-identityprotection.md) et déclenchez la vérification en deux étapes en fonction des événements à risque.

## <a name="next-steps"></a>Étapes suivantes

- Se procurer un [plan de déploiement](https://aka.ms/MFADeploymentPlan) MFA détaillé

- Trouver des informations détaillées sur la [Gestion des licences de vos utilisateurs](concept-mfa-licensing.md)

- Se renseigner plus précisément sur [les versions à déployer](concept-mfa-whichversion.md)

- Obtenir des réponses aux [Questions fréquentes (FAQ)](multi-factor-authentication-faq.md)
