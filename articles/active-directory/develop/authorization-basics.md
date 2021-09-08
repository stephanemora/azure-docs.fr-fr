---
title: Notions de base des autorisations | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les principes de base de l’autorisation dans la Plateforme d’identités Microsoft.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: 4dcdb90daa9bb8cd1df79ec399eb9372fca02c61
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037187"
---
# <a name="authorization-basics"></a>Notions de base des autorisations

L’**autorisation** (parfois abrégée en *AuthZ* en anglais) définit les autorisations qui sont utilisées pour évaluer l'accès aux ressources ou aux fonctionnalités.  L’**authentification**, quant à elle, (parfois abrégée en *AuthN*) vise à prouver qu’une entité, par exemple un utilisateur ou un service, est bien ce qu’elle prétend être.

L’autorisation peut spécifier les fonctionnalités (ou ressources) auxquelles une entité est autorisée à accéder ou les données auxquelles cette entité peut accéder, et ce qu’elle peut faire avec ces données. Dans ce cas, on parle souvent de *contrôle d’accès*.

> [!NOTE]
> L’authentification et l’autorisation sont des concepts qui ne sont pas limités aux seuls utilisateurs. Les services ou les applications de démon sont souvent générés pour faire des demandes de ressources en leur nom plutôt qu’au nom d’un utilisateur spécifique. Lorsque vous discutez de ces rubriques, le terme « entité » fait référence à un utilisateur ou à une application.


## <a name="authorization-approaches"></a>Approches liées aux autorisations

Il existe plusieurs approches courantes pour gérer les autorisations. Le [contrôle d’accès en fonction du rôle](./custom-rbac-for-developers.md) est actuellement l’approche la plus courante utilisant la Plateforme d’identités Microsoft.


### <a name="authentication-as-authorization"></a>Authentification et autorisation 

La forme d’autorisation la plus simple est peut-être d’accorder ou de refuser l’accès selon que l’entité qui effectue une demande a été authentifiée ou non. Si le demandeur peut prouver qu’il est bien celui qu’il prétend être, il peut accéder aux ressources ou aux fonctionnalités protégées.

### <a name="access-control-lists"></a>Listes de contrôle d'accès

L’autorisation via des listes de contrôle d’accès (ACL) implique la conservation de listes explicites d’entités spécifiques qui ont ou n’ont pas accès à une ressource ou à une fonctionnalité. Les ACL offrent un contrôle plus précis de l’authentification en tant qu’autorisation, mais deviennent difficiles à gérer à mesure que le nombre d’entités augmente.

### <a name="role-based-access-control"></a>Contrôle d'accès en fonction d'un rôle 

Le contrôle d’accès en fonction du rôle (RBAC) est probablement l’approche la plus courante pour appliquer les autorisations dans les applications. Lorsque vous utilisez le RBAC, les rôles sont définis pour décrire les types d’activités qu’une entité peut effectuer. Un développeur d’applications accorde l’accès aux rôles plutôt qu’aux entités individuelles. Un administrateur peut ensuite attribuer des rôles à différentes entités pour contrôler lesquelles ont accès à quelles ressources et fonctionnalités.

Dans les implémentations du RBAC avancé, les rôles peuvent être mappés à des collections d’autorisations, où une autorisation décrit une action ou une activité granulaire qui peut être effectuée. Les rôles sont ensuite configurés en tant que combinaisons d’autorisations. Vous calculez le jeu d’autorisations global des entités pour une application en croisant les autorisations accordées aux différents rôles attribués à l’entité. Un bon exemple de cette approche est l’implémentation du RBAC qui régit l’accès aux ressources dans les abonnements Azure.

> [!NOTE]
> Le [RBAC des applications](./custom-rbac-for-developers.md) diffère du [RBAC d’Azure](/azure/role-based-access-control/overview) et du [RBAC d’Azure AD](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Les rôles personnalisés d’Azure et les rôles intégrés font tous deux partie du RBAC d’Azure, qui vous aide à gérer les ressources Azure. Le RBAC d’Azure AD vous permet de gérer les ressources Azure AD.

### <a name="attribute-based-access-control"></a>Contrôle d’accès en fonction de l’attribut 

Le contrôle d’accès en fonction de l’attribut (ABAC) est un mécanisme de contrôle d’accès plus précis. Dans cette approche, les règles sont appliquées aux attributs de l’entité, aux ressources faisant l’objet de l’accès et à l’environnement actuel pour déterminer si l’accès à certaines ressources ou fonctionnalités est autorisé. Par exemple, vous pouvez uniquement autoriser les utilisateurs qui sont des managers à accéder aux fichiers identifiés avec une balise de métadonnées « managers pendant les heures de travail uniquement » entre 9H00 et 17H00 les jours ouvrables. Dans ce cas, l’accès est déterminé en examinant l’attribut de l’utilisateur (statut : manager), l’attribut de la ressource (balise de métadonnées sur un fichier) et également l’attribut d’environnement (l’heure actuelle).

L’un des avantages de l’ABAC est qu’il est possible d’obtenir un contrôle d’accès plus granulaire et dynamique grâce à des évaluations de règles et de conditions sans avoir à créer un grand nombre de rôles et d’affectations de RBAC très spécifiques.

L’une des méthodes permettant d’obtenir un ABAC avec Azure Active Directory consiste à utiliser des [groupes dynamiques](../enterprise-users/groups-create-rule.md). Les groupes dynamiques permettent aux administrateurs d’affecter dynamiquement des utilisateurs à des groupes basés sur des attributs utilisateur spécifiques avec les valeurs souhaitées.  Par exemple, un groupe Auteurs peut être créé là où tous les utilisateurs ayant comme fonction Auteur sont affectés dynamiquement au groupe Auteurs.  Les groupes dynamiques peuvent être utilisés en association avec le RBAC pour l’autorisation dans laquelle vous mappez des rôles à des groupes et attribuez dynamiquement des utilisateurs à des groupes.

[Azure ABAC](../../role-based-access-control/conditions-overview.md) est un exemple de solution ABAC disponible actuellement. Azure ABAC s’appuie sur Azure RBAC en ajoutant des conditions d’attribution de rôle en fonction des attributs dans le contexte d’actions spécifiques. 

## <a name="implementing-authorization"></a>Implémentation des autorisations

La logique d’autorisation est souvent implémentée dans les applications ou les solutions où le contrôle d’accès est requis. Dans de nombreux cas, les plateformes de développement d’applications proposent des intergiciels (middleware) ou d’autres solutions d’API qui simplifient l’implémentation des autorisations. Par exemple, l’utilisation de [AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) dans ASP.NET ou de [Route Guards](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window) dans Angular.

Pour les approches d’autorisation qui reposent sur les informations relatives à l’entité authentifiée, une application évalue les informations échangées lors de l’authentification. Par exemple, en utilisant les informations fournies dans un [jeton de sécurité](./security-tokens.md). Pour les informations non contenues dans un jeton de sécurité, une application peut effectuer des appels supplémentaires à des ressources externes.

Il n’est pas strictement nécessaire pour les développeurs d’incorporer la logique d’autorisation complète dans leurs applications. À la place, les services d’autorisation dédiés peuvent être utilisés pour centraliser l’implémentation et la gestion des autorisations.


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’implémentation personnalisée d’un contrôle d’accès en fonction du rôle dans les applications, consultez [Contrôle d’accès en fonction du rôle pour les développeurs d’applications](./custom-rbac-for-developers.md).
- Pour plus d’informations sur le processus d’inscription d’une application lui permettant de s’intégrer avec la Plateforme d’identités Microsoft, consultez [Modèle d’application](./application-model.md).
- Pour obtenir un exemple de configuration d’une autorisation basée sur l’authentification simple, consultez [Configurer votre application App Service ou Azure Functions pour utiliser la connexion Azure AD](/azure/app-service/configure-authentication-provider-aad).
