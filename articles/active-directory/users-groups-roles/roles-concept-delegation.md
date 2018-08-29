---
title: Déléguer des rôles d’administrateur dans Azure Active Directory | Microsoft Docs
description: modèles de délégation, exemples et rôle de sécurité dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209200"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Déléguer l’administration dans Azure Active Directory

La croissance des organisations s’accompagne d’une complexité accrue, à laquelle il est courant de répondre en réduisant une partie de la surcharge de la gestion de l’accès avec les rôles d’administrateur Azure Active Directory (AD). Vous pouvez attribuer le plus petit des privilèges aux utilisateurs afin qu’ils puissent accéder à leurs applications et effectuer leurs tâches. Vous ne souhaiterez peut-être pas attribuer le rôle d’administrateur général à chaque propriétaire d’application, mais ce faisant la gestion des applications peut incomber aux administrateurs généraux existants. De nombreuses raisons peuvent amener une organisation à opter pour une administration plus décentralisée. Cet article peut vous aider à planifier la délégation dans votre organisation.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>Autorisations centralisées et autorisations déléguées

À mesure que grandit une organisation, il peut être difficile de suivre les utilisateurs qui ont des rôles d’administrateur spécifiques. Une organisation peut être vulnérable aux failles de sécurité si un employé a des droits d’administrateur qu’il ne devrait pas détenir. En règle générale, le nombre d’administrateurs et la granularité de leurs autorisations dépendent de la taille et de la complexité du déploiement.

* Dans les déploiements petits ou de preuve de concept, un ou quelques administrateurs effectuent toutes les tâches ; il n’existe aucune délégation. Dans ce cas, créez chaque administrateur en lui attribuant le rôle Administrateur général.
* Dans les déploiements plus grands comportant davantage de machines, d’applications et de postes de travail, plus de délégation est nécessaire. Plusieurs administrateurs peuvent avoir des responsabilités fonctionnelles plus spécifiques (rôles). Par exemple, certains peuvent être administrateurs d’identités privilégiées, d’autres administrateurs d’application. En outre, un administrateur peut gérer uniquement certains groupes d’objets tels que les appareils.
* Les déploiements encore plus grands peuvent nécessiter des autorisations plus granulaires et, éventuellement, des administrateurs ayant des rôles non conventionnels ou hybrides.

Dans le portail Azure AD, vous pouvez [voir tous les membres de n’importe quel rôle](directory-manage-roles-portal.md), ce qui peut vous aider à vérifier rapidement le déploiement et à déléguer les autorisations.

Si vous êtes intéressé par la délégation de l’accès aux ressources Azure et par l’accès non administratif dans Azure AD, consultez [Attribuer un rôle RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planification de la délégation

Bien que la difficulté consiste à développer un modèle de délégation adapté aux besoins uniques de votre organisation, il existe des modèles très simples que vous pouvez appliquer avec peu de modifications. Le développement d’un modèle de délégation étant un processus de conception itératif, nous vous suggérons de suivre ces étapes :

* Définir les rôles dont vous avez besoin
* Déléguer l’administration des applications
* Accorder la possibilité d’inscrire des applications
* Déléguer l’appartenance des applications
* Développer un plan de sécurité
* Établir des comptes d’urgence
* Sécuriser vos rôles d’administrateur
* Rendre l’élévation de privilège temporaire

## <a name="define-roles"></a>Définir des rôles

Déterminez quelles tâches Active Directory sont exécutées par les administrateurs et comment ces tâches sont mappées aux rôles. Par exemple, la création de sites Active Directory est une tâche d’administration de service, tandis que la modification de l’appartenance aux groupes de sécurité relève généralement de l’administration des données. Vous pouvez [voir des descriptions détaillées des rôles](directory-manage-roles-portal.md) dans le portail Azure.

Chaque tâche doit être évaluée du point de vue de la fréquence, de l’importance et de la difficulté. Il s’agit là des aspects vitaux de la définition de tâche, car ils déterminent si une autorisation doit être déléguée. Les tâches qui sont exécutées de façon routinière, qui présentent un risque limité et qui sont très faciles à effectuer sont d’excellents candidats à la délégation. Par contre, les tâches qui sont rarement exécutées, mais qui ont un impact important dans l’organisation et qui nécessitent des niveaux élevés de compétences doivent faire l’objet d’une attention particulière en vue de leur délégation. Au lieu de cela, vous pouvez [temporairement élever un compte au rôle requis](../active-directory-privileged-identity-management-configure.md) ou réattribuer la tâche.

## <a name="delegate-app-administration"></a>Déléguer l’administration des applications

La prolifération d’applications au sein de votre organisation peut restreindre votre modèle de délégation. S’il fait peser la gestion de l’accès aux applications sur l’administrateur général, sa surcharge est susceptible d’augmenter au fil du temps. Si vous avez accordé à des personnes le rôle Administrateur général pour des opérations telles que la configuration d’applications d’entreprise, vous pouvez désormais affecter ces opérations aux rôles les moins privilégiés suivants. Cette action permet d’améliorer votre situation de sécurité et de réduire le risque d’erreurs malheureuses. Les rôles d’administrateur d’application les plus privilégiés sont les suivants :

* Le rôle **Administrateur d’application**, qui donne la possibilité de gérer toutes les applications dans l’annuaire, y compris les inscriptions, les paramètres d’authentification unique, la gestion des licences et les affectations d’utilisateurs et de groupes, les paramètres de proxy d’application et le consentement. Il n’accorde pas la possibilité de gérer l’accès conditionnel.
* Le rôle **Administrateur d’application cloud**, qui accorde toutes les capacités de l’administrateur d’application, à ceci près qu’il n’accorde pas l’accès aux paramètres de proxy d’application (car il n’a aucune autorisation locale).### Déléguer les autorisations de propriétaire d’application par application

## <a name="delegate-app-registration"></a>Déléguer l’inscription des applications

Par défaut, tous les utilisateurs peuvent créer des inscriptions d’application. Si vous souhaitez accorder de manière sélective la possibilité de créer des inscriptions d’application, vous devez définir l’option **Les utilisateurs peuvent inscrire des applications** sur Non dans les paramètres utilisateur, puis attribuer le rôle Développeur d’applications. Ce rôle donne la possibilité de créer des inscriptions d’application uniquement quand l’option **Les utilisateurs peuvent inscrire des applications** est désactivée. Les développeurs d’applications peuvent également donner leur consentement pour eux-mêmes quand l’option **Les utilisateurs peuvent autoriser les applications à accéder aux données d’entreprise en leur nom** est définie sur Non. Quand un développeur d’applications crée une inscription d’application, il est automatiquement ajouté en tant que premier propriétaire.

## <a name="delegate-app-ownership"></a>Déléguer l’appartenance des applications

Pour affiner la délégation de l’accès aux applications, vous pouvez attribuer la propriété à des applications d’entreprise spécifiques. Vous complétez ainsi la prise en charge existante de l’affectation des propriétaires d’inscription d’application. La propriété est affectée par application d’entreprise dans le panneau des applications d’entreprise. L’avantage est que les propriétaires peuvent gérer uniquement les applications d’entreprise qu’ils possèdent. Par exemple, vous pouvez affecter un propriétaire pour l’application Salesforce ; ce propriétaire peut ainsi gérer l’accès à Salesforce et sa configuration, à l’exclusion de toute autre application. Une application d’entreprise peut avoir de nombreux propriétaires, et un utilisateur peut être le propriétaire de nombreuses applications d’entreprise. Il existe deux rôles de propriétaire d’application :

* Le rôle **Propriétaire d’application d’entreprise** accorde la possibilité de gérer les applications d’entreprise détenues par l’utilisateur, y compris les paramètres d’authentification unique, les affectations d’utilisateurs et de groupes et l’ajout de propriétaires. Il n’accorde pas la possibilité de gérer les paramètres de proxy d’application ou l’accès conditionnel.
* Le rôle **Propriétaire d’inscription d’application** accorde la possibilité de gérer les inscriptions d’application pour l’application détenue par l’utilisateur, y compris le manifeste d’application et l’ajout de propriétaires.

## <a name="develop-a-security-plan"></a>Développer un plan de sécurité

Azure AD fournit un guide détaillé de la planification et de l’exécution d’un plan de sécurité sur vos rôles d’administrateur Azure AD, [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud](directory-admin-roles-secure.md). 

## <a name="establish-emergency-accounts"></a>Établir des comptes d’urgence

Pour conserver l’accès à votre magasin de gestion d’identité quand un problème survient, préparez des comptes d’accès d’urgence comme indiqué dans [Créer des comptes d’administration de l’accès d’urgence](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Sécuriser vos rôles d’administrateur

Les attaquants qui obtiennent le contrôle de comptes privilégiés peuvent causer des dommages considérables ; vous devez donc protéger ces comptes en premier, à l’aide de la [stratégie d’accès de référence](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) qui est disponible par défaut pour tous les locataires Azure AD (en préversion publique). La stratégie met en œuvre l’authentification multifacteur sur les comptes Azure AD privilégiés. Les rôles Azure AD suivants sont couverts par la stratégie de référence Azure AD :

* Administrateur général
* Administrateur SharePoint
* Administrateur Exchange
* Administrateur de l’accès conditionnel
* Administrateur de sécurité

## <a name="elevate-privilege-temporarily"></a>Élever le privilège temporairement

Pour la plupart des activités quotidiennes qu’ils accomplissent, tous les utilisateurs n’ont pas besoin des droits d’administrateur général. En outre, tous les utilisateurs ne doivent pas être définitivement affectés au rôle Administrateur général. Ainsi, quand les utilisateurs ont besoin d’agir en tant qu’administrateur général, ils doivent activer l’attribution de rôle dans Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) sur leur propre compte ou sur un autre compte d’administration.

## <a name="next-steps"></a>Étapes suivantes

Pour des informations de référence sur les descriptions de rôle Azure AD, consultez [Attribuer des rôles d’administrateur dans Azure AD](directory-assign-admin-roles.md).