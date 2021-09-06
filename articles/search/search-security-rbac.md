---
title: Autorisation basée sur des rôles
titleSuffix: Azure Cognitive Search
description: Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour obtenir des autorisations granulaires sur l’administration du service et les tâches de contenu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563480"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Utiliser l’autorisation basée sur les rôles dans Azure Recherche cognitive

Azure fournit un système d'autorisation global de[contrôle d'accès basé sur les rôles](../role-based-access-control/role-assignments-portal.md) (RBAC) pour tous les services fonctionnant sur la plateforme. Dans Recherche cognitive, vous pouvez utiliser l’autorisation de rôle de l’une des manières suivantes :

+ Autorisez l'accès aux opérations du plan de contrôle, telles que l'ajout de capacité ou la rotation des clés, sur le service de recherche lui-même par le biais des rôles de Propriétaire, de Contributeur et de Lecteur.

+ Autorisez l’accès aux opérations du plan de données, telles que la création ou l’interrogation d’index. Cette fonctionnalité est actuellement disponible en préversion publique ( [sur demande](https://aka.ms/azure-cognitive-search/rbac-preview) ). Une fois votre abonnement intégré, suivez les instructions de cet article pour utiliser la fonctionnalité.

+ Autorisez les connexions d’indexeur sortant à être établies à [l’aide d’une identité gérée](search-howto-managed-identities-data-sources.md). Pour un service de recherche auquel une identité gérée est affectée, vous pouvez créer des attributions de rôles qui étendent les services de données externes, tels que les Stockage d’objets Blob Azure, pour autoriser l’accès en lecture sur les objets blob par votre service de recherche approuvé.

Cet article se concentre sur les deux premières puces, les rôles pour les opérations de plan de contrôle et de plan de données. Pour plus d’informations sur les appels de l’indexeur sortant, commencez par [Configurer une identité gérée](search-howto-managed-identities-data-sources.md).

Quelques scénarios RBAC **ne sont pas** directement pris en charge, notamment :

+ [Rôles personnalisés](../role-based-access-control/custom-roles.md)

+ Accès à l’identité de l’utilisateur via les résultats de la recherche (parfois appelé sécurité au niveau des lignes ou sécurité au niveau du document)

  > [!Tip]
  > Pour un accès en fonction de l’identité sur les résultats de recherche, vous pouvez créer des [filtres de sécurité](search-security-trimming-for-azure-search.md) pour ajuster les résultats par identité, en supprimant les documents auxquels le demandeur ne doit pas avoir accès.
  >

## <a name="roles-used-in-search"></a>Rôles utilisés dans la Recherche

Les rôles intégrés incluent les rôles généralement disponibles et en version préliminaire, dont l’appartenance affectée est constituée de Azure Active Directory utilisateurs et groupes.

Les attributions de rôles sont cumulatives et omniprésentes pour tous les outils et bibliothèques clientes utilisés pour créer ou gérer un service de recherche. Les Clients incluent les Portail Azure, l’API REST de Gestion, les Azure PowerShell, les Azure CLI et la bibliothèque cliente de gestion des kits de développement logiciel (sdk) Azure. 

Il n’existe aucune restriction régionale, de niveau ou de tarification pour l’utilisation de RBAC sur Azure Recherche cognitive, mais votre service de recherche doit se trouver dans le cloud public Azure.

| Role | Statut | S’applique à | Description |
| ---- | -------| ---------- | ----------- |
| [Propriétaire](../role-based-access-control/built-in-roles.md#owner) | Stable | Plan de contrôle | Accès complet à la ressource, y compris la possibilité d’affecter des rôles Azure. Les administrateurs d’abonnements sont membres par défaut. |
| [Contributeur](../role-based-access-control/built-in-roles.md#contributor) | Stable | Plan de contrôle | Même niveau d’accès que le propriétaire, moins la possibilité d’affecter des rôles. |
| [Lecteur](../role-based-access-control/built-in-roles.md#reader) | Stable | Plan de contrôle | Accès limité à des informations de service partielles. Sur le portail, le rôle Lecteur permet d'accéder aux informations de la page de présentation du service, de la section Éléments principaux et de l'onglet Surveillance. Tous les autres onglets et pages sont inaccessibles. </br></br>Ce rôle a accès aux informations de service : groupe de ressources, état du service, emplacement, nom et ID de l’abonnement, balises, URL, niveau tarifaire, réplicas, partitions et unités de recherche. </br></br>Ce rôle a aussi accès aux métriques de service : latence de recherche, pourcentage de demandes limitées, nombre moyen de requêtes par seconde. </br></br>Il n’y a pas d’accès aux clés API, aux attributions de rôles, au contenu (index ou cartes de synonymes) ou aux métriques de contenu (stockage consommé, nombre d’objets). |
| [Contributeur du service de recherche](../role-based-access-control/built-in-roles.md#search-service-contributor) | Préversion | Plan de contrôle | Fournit un accès complet au service de recherche et aux définitions d’objets, mais aucun accès aux données indexées. Ce rôle est destiné aux administrateurs de service qui ont besoin de plus d’informations que le rôle lecteur, mais qui ne doivent pas avoir accès au contenu de la carte d’index ou de synonymes.|
| [Contributeur de données d’index de recherche](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Préversion | Plan de données | Fournit un accès complet aux données d’index, mais rien d’autre. Ce rôle est destiné aux développeurs ou aux propriétaires d’index chargés de créer et de charger du contenu, mais qui ne doivent pas avoir accès aux informations du service de recherche. L’étendue correspond à toutes les ressources de niveau supérieur (indexeurs, cartes de synonymes, indexeurs, sources de données, compétences) sur le service de recherche. |
| [Lecteur de données d’index de recherche](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Préversion | Plan de données | Fournit un accès en lecture seule aux données d’index. Ce rôle est destiné aux utilisateurs qui exécutent des requêtes sur un index. L’étendue correspond à toutes les ressources de niveau supérieur (indexeurs, cartes de synonymes, indexeurs, sources de données, compétences) sur le service de recherche. |

## <a name="scope-control-plane-and-data-plane"></a>Portée  : Plan de contrôle et plan de données

Les ressources Azure ont le concept de catégories d'opérations du [plan de contrôle et du plan de données](../azure-resource-manager/management/control-plane-and-data-plane.md). Les rôles intégrés pour Recherche cognitive s’appliquent à un plan ou à l’autre.

| Category | Operations |
|----------|------------|
| Plan de contrôle | Les opérations incluent la création, la mise à jour et la suppression de services, la gestion des clés API, l’ajustement des partitions et des réplicas, etc. L'[API REST de gestion](/rest/api/searchmanagement/) et les bibliothèques clientes équivalentes définissent les opérations applicables au plan de contrôle. |
| Plan de données | Opérations sur le point de terminaison du service de recherche, englobant tous les objets et données hébergés sur le service. L’indexation, l’interrogation et toutes les actions associées ciblent le plan de données, accessible via l' [API REST de recherche](/rest/api/searchservice/) et les bibliothèques clientes équivalentes. </br></br>Actuellement, vous ne pouvez pas utiliser les attributions de rôles pour restreindre l’accès à des index individuels, des cartes de synonymes, des indexeurs, des sources de données ou des compétences. |

## <a name="configure-search-for-data-plane-authentication"></a>Configurer la recherche de l’authentification du plan de données

Si vous utilisez l’un des rôles de plan de données en préversion (index de recherche de données contributeur ou index de recherche) et l’authentification Azure AD, votre service de recherche doit être configuré pour reconnaître un en-tête **d’autorisation** sur les demandes de données qui fournissent un jeton d’accès OAuth2. Cette section explique comment configurer votre service de recherche. Si vous utilisez des rôles de plan de contrôle (propriétaire, contributeur, lecteur), vous pouvez ignorer cette étape.

Avant de commencer, [inscrivez-vous](https://aka.ms/azure-cognitive-search/rbac-preview) à la version préliminaire RBAC. Votre abonnement doit être inscrit au programme avant que vous puissiez utiliser cette fonctionnalité. Le traitement de la demande d’inscription de processus peut prendre jusqu’à deux jours ouvrables. Vous recevrez un courriel lorsque votre service sera prêt.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/config-svc-portal)

1. Ouvrez le portail avec la syntaxe suivante : [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true).

1. Accéder à votre service de recherche.

1. Sélectionnez **Clés** dans le volet de navigation de gauche.

1. Choisissez un mécanisme de **contrôle d'accès à l'API**. Si vous ne voyez pas ces options, vérifiez l’URL du portail. Si vous ne pouvez pas enregistrer votre sélection, il y a un problème avec l’inscription de l’abonnement. 

   | Option | Statut | Description |
   |--------|--------|-------------|
   | Clé d’API | Généralement disponible (par défaut) | Requiert un [administrateur ou des clés API](search-security-api-keys.md) de requête dans l'en-tête de la demande pour l'autorisation. Aucun rôle n’est utilisé. |
   | Contrôle d’accès en fonction du rôle | Préversion | Nécessite l’appartenance à une attribution de rôle pour terminer la tâche, décrite dans l’étape suivante. Chaque requête nécessite un en-tête d’autorisation. Le choix de cette option vous limite aux clients qui prennent en charge l’API REST 2021-04-30-preview. |
   | Les deux | Préversion | Les demandes sont valides à l’aide d’une clé API ou d’un jeton d’autorisation. |

Une fois que votre service de recherche est activé pour le RBAC, le portail requiert l’indicateur de fonctionnalité dans l’URL pour assigner des rôles et afficher du contenu. **Le contenu, tel que les index et les indexeurs, est visible uniquement dans le portail si vous l’ouvrez avec l’indicateur de fonctionnalité.** Si vous souhaitez restaurer le comportement par défaut à une date ultérieure, rétablissez la sélection des clés API sur les **clés API**.

### <a name="rest-api"></a>[**API REST**](#tab/config-svc-rest)

Utilisez l’API REST de gestion, version 2021-04-01-Preview, pour configurer votre service.

1. Appeler le service de [création ou de mise à jour](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

1. Définir [DataPlaneAuthOptions ](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) à `aadOrApiKey`. Voir [cet exemple](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions) pour la syntaxe.

1. Définissez [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) pour spécifier si les erreurs 401 ou 403 sont renvoyées lorsque l'authentification échoue.

---

## <a name="assign-roles"></a>Attribuer des rôles

Les rôles peuvent être attribués à l'aide de l'une des [approches prises](../role-based-access-control/role-assignments-steps.md) en charge décrites dans la documentation sur le contrôle d'accès basé sur les rôles d'Azure.

Vous devez être un propriétaire ou avoir [des autorisations Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) pour gérer les attributions de rôles.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/rbac-portal)

Définissez l'indicateur de fonctionnalité sur l'URL du portail pour travailler avec les rôles d'aperçu : Contributeur de service de recherche, Contributeur de données d'index de recherche, et Lecteur de données d'index de recherche.

1. Ouvrez le portail avec la syntaxe suivante : [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). Vous devriez voir `feature.enableRbac=true` dans l'URL.

1. Accéder à votre service de recherche.

1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de navigation de gauche.

1. Sur le côté droit, sous **Autoriser l'accès à cette ressource**, sélectionnez **Ajouter une attribution de rôle**.

1. Recherchez un rôle applicable (Propriétaire, Collaborateur, Lecteur, Search Service Contributeur, Index de données de recherche, Collaborateur de données d’index de recherche), puis affectez une identité d’utilisateur ou de groupe Azure Active Directory.

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

Lorsque vous [utilisez PowerShell pour attribuer des rôles](../role-based-access-control/role-assignments-powershell.md), appelez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) en indiquant le nom de l'utilisateur ou du groupe Azure et la portée de l'attribution.

Avant de commencer, veillez à charger les modules Azure et AzureAD et à vous connecter à Azure :

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

Dans le cadre du service, votre syntaxe doit ressembler à l’exemple suivant :

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

Limité à un index individuel :

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

Rappelez-vous que vous pouvez uniquement limiter l’accès à des ressources de niveau supérieur, telles que des index, des cartes de synonymes, des indexeurs, des sources de données et des compétences. Vous ne pouvez pas contrôler l’accès aux documents de recherche (contenu d’index) avec des rôles Azure.

---

## <a name="configure-requests-and-test"></a>Configurer les demandes et tester

Pour effectuer un test par programme, modifiez votre code pour utiliser une API REST de recherche (toute version prise en charge) et définissez l’en-tête Authorization sur les demandes. Si vous utilisez l’un des kits de développement logiciel (SDK) Azure, consultez leurs versions bêta pour voir si l’en-tête d’autorisation est disponible. 

Selon votre application, une configuration supplémentaire est requise pour inscrire une application auprès de Azure Active Directory ou pour obtenir et passer des jetons d’autorisation.

Vous pouvez également utiliser les Portail Azure et les rôles qui vous sont attribués pour le test :

1. Ouvrez le portail avec la syntaxe suivante : [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true). 

   Même si votre service est compatible RBAC à une étape précédente, le portail exige que l’indicateur de fonctionnalité appelle des comportements RBAC. **Le contenu, tel que les index et les indexeurs, est visible uniquement dans le portail si vous l’ouvrez avec l’indicateur de fonctionnalité.** Si vous souhaitez restaurer le comportement par défaut à une date ultérieure, rétablissez la sélection des clés API sur les **clés API**.

1. Accéder à votre service de recherche.

1. Dans la page de la vue d’ensemble, sélectionnez l’onglet **Index** :

   + Pour l'adhésion à Search Index Data Reader, utilisez Search Explorer pour interroger l'index. Vous pouvez utiliser n’importe quelle version d’API pour vérifier l’accès.

   + Pour le contributeur de données d'index de recherche, sélectionnez **Nouvel Index** pour créer un nouvel index. L’enregistrement d’un nouvel index permet de vérifier l’accès en écriture au service.

## <a name="disable-api-key-authentication"></a>Désactiver l'authentification par clé API

Les clés API ne peuvent pas être supprimées, mais elles peuvent être désactivées sur votre service. Si vous utilisez les rôles de contributeur de données d'index de recherche et de lecteur de données d'index de recherche et l'authentification Azure AD, vous pouvez désactiver les clés API, ce qui a pour effet que le service de recherche refuse toutes les demandes liées aux données qui fournissent une clé.

Utilisez l’API REST de gestion de préversion, version 2021-04-01-Preview, pour cette tâche.

1. Définir [DataPlaneAuthOptions ](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) à `aadOrApiKey`.

1. [Attribuez les rôles](#assign-roles) et vérifiez qu'ils fonctionnent correctement.

1. Définissez `disableLocalAuth` sur **True**.

Si vous revenez sur la dernière étape, en définissant `disableLocalAuth` en **False** , le service de recherche recommencera à accepter automatiquement les clés API lors de la demande (en supposant qu'elles soient spécifiées).