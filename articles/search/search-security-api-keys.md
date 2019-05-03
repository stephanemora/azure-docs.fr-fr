---
title: Créer, gérer et sécuriser des clés API d’administration et de requête - Recherche Azure
description: Les clés API contrôlent l’accès au point de terminaison de service. Les clés d’administration accordent un accès en écriture. Les clés de requête peuvent être créées pour l’accès en lecture seule.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: f12d7f1a7dfcaf80df9a71a0bfc598d72db63a2e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024410"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Créer et gérer des clés API pour un service Recherche Azure

Toutes les demandes adressées à un service de recherche ont besoin d’une clé API en lecture seule générée spécialement pour votre service. Cette clé API constitue le seul mécanisme d’authentification de l’accès au point de terminaison de votre service de recherche et doit être incluse dans chaque demande. Dans les [solutions REST](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), la clé API est généralement spécifiée dans un en-tête de demande. Dans les [solutions .NET](search-howto-dotnet-sdk.md#core-scenarios), une clé est souvent spécifiée sous forme de paramètre de configuration, puis transmise en tant que [Credentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (clé d’administration) ou de [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (clé de requête) sur [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Lors du provisionnement du service, les clés sont créées avec votre service de recherche. Vous pouvez afficher et obtenir des valeurs de clés dans le [portail Azure](https://portal.azure.com).

![Page du portail, section Paramètres\Clés](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Qu’est-ce qu’une clé API ?

Une clé API est une chaîne composée de nombres et de lettres générée de manière aléatoire. Par le biais des [autorisations basées sur le rôle](search-security-rbac.md), vous pouvez supprimer ou lire les clés, mais vous ne pouvez pas remplacer une clé avec un mot de passe défini par l’utilisateur ou utiliser Active Directory en tant que méthode d’authentification principale pour accéder aux opérations de recherche. 

Deux types de clés sont utilisés pour accéder à votre service de recherche : administration (lecture-écriture) et requête (lecture seule).

|Clé|DESCRIPTION|limites|  
|---------|-----------------|------------|  
|Admin|Accorde des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données.<br /><br /> Deux clés d’administration, appelées clés *principale* et *secondaire* dans le portail, sont générées quand le service est créé et peuvent être régénérées individuellement à la demande. La possession de deux clés permet de substituer une clé quand l’autre est utilisée pour un accès continu au service.<br /><br /> Les clés d’administration sont spécifiées uniquement dans les en-têtes de requête HTTP. Vous ne pouvez pas insérer de clé API d’administration dans une URL.|2 max. par service|  
|Requête|Accorde un accès en lecture seule aux index et aux documents. Ces clés sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.<br /><br /> Les clés de requête sont créées à la demande. Vous pouvez les créer manuellement dans le portail ou par programme via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Les clés de requête peuvent être spécifiées dans un en-tête de requête HTTP pour les opérations de recherche, de suggestion ou de consultation. Vous pouvez également transmettre une clé de requête en tant que paramètre pour une URL. Selon la façon dont votre application cliente formule la demande, il peut être plus facile de transmettre la clé en tant que paramètre de requête :<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 par service|  

 Visuellement, il n’existe aucune distinction entre une clé d’administration et une clé de requête. Les deux clés sont des chaînes composées de 32 caractères alphanumériques générés de façon aléatoire. Si vous n’êtes pas sûr du type de clé spécifié dans votre application, vous pouvez [vérifier les valeurs de clé dans le portail](https://portal.azure.com) ou utiliser l’[API REST](https://docs.microsoft.com/rest/api/searchmanagement/) pour retourner la valeur et le type de clé.  

> [!NOTE]  
>  L’insertion de données sensibles comme une `api-key` dans l’URI de requête est considérée comme une pratique peu sécurisée. C’est pourquoi Recherche Azure accepte uniquement une clé de requête sous forme de `api-key` dans la chaîne de requête, et il est conseillé de procéder autrement, sauf si le contenu de l'index doit être accessible au public. En règle générale, nous vous recommandons de transmettre votre `api-key` en tant qu'en-tête de demande.  

## <a name="find-existing-keys"></a>Rechercher des clés existantes

Vous pouvez obtenir les clés d’accès dans le portail ou via l’[API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/). Pour plus d’informations, consultez la page [Gérer les clés API d’administration et de requête](search-security-api-keys.md).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
3. Sélectionnez le service et dans la page Vue d’ensemble, cliquez sur **paramètres** >**clés** pour afficher les clés d’administration et de requête.

   ![Page du portail, section Paramètres\Clés](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Créer des clés de requête

Clés de requête sont utilisés pour l’accès en lecture seule à des documents dans un index pour les opérations ciblant une collection de documents. Requêtes de recherche, de filtrage et de suggestion sont toutes les opérations qui prennent une clé de requête. Toute opération en lecture seule qui renvoie les définitions de données ou un objet, par exemple, un index définition ou l’indexeur, le système nécessite une clé d’administration.

Restriction des accès et des opérations dans les applications clientes est essentiel à protéger les ressources de recherche sur votre service. Toujours utiliser une clé de requête plutôt que d’une clé d’administration pour toute requête d’origine à partir d’une application cliente.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
3. Sélectionnez le service et dans la page Vue d’ensemble, cliquez sur **paramètres** >**clés**.
4. Cliquez sur **gérer les clés de requête**.
5. Utiliser la clé de requête déjà générée pour votre service, ou créer de nouvelles clés de requête jusqu'à 50. La clé de requête par défaut n’est pas nommée, mais les clés de requête supplémentaires peuvent être nommés pour la facilité de gestion.

   ![Créer ou utiliser une clé de requête](media/search-security-overview/create-query-key.png) 

> [!Note]
> Vous trouverez un exemple de code illustrant l’utilisation de la clé de requête dans [interroger un index Azure Search dans C# ](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Régénération des clés d’administration

Deux clés d’administration sont créés pour chaque service afin que vous pouvez faire pivoter une clé primaire, à l’aide de la clé secondaire pour la continuité d’activité.

1. Dans la page **Paramètres** >**Clés**, copiez la clé secondaire.
2. Pour toutes les applications, mettez à jour les paramètres de la clé API afin d’utiliser la clé secondaire.
3. Régénérez la clé principale.
4. Mettez à jour toutes les applications pour qu’elles utilisent la nouvelle clé principale.

Si vous régénérez par inadvertance les deux clés en même temps, toutes les demandes de client à l’aide de ces clés échoue avec HTTP 403 Interdit. Toutefois, contenu n’est pas supprimé et que vous n’êtes pas définitivement verrouillé. 

Vous pouvez toujours accéder au service via le portail ou de la couche de gestion ([API REST](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell), ou Azure Resource Manager). Fonctions de gestion sont opérationnel via un ID d’abonnement pas un service clé api et sont donc toujours disponibles, même si vos clés d’api ne sont pas. 

Une fois que vous créez de nouvelles clés via la couche de portail ou de gestion, l’accès est restauré à votre contenu (index, indexeurs, sources de données, les cartes de synonymes) une fois que vous avez les nouvelles clés et fournir ces clés sur les demandes.

## <a name="secure-api-keys"></a>Sécuriser les clés API
La sécurité des clés est assurée en limitant l’accès via le portail ou des interfaces Resource Manager (PowerShell ou interface de ligne de commande). Comme indiqué, les administrateurs des abonnements peuvent afficher et régénérer toutes les clés API. Par précaution, passez en revue les affectations de rôle pour comprendre qui a accès aux clés Admin.

+ Dans le tableau de bord de service, cliquez sur **Contrôle d’accès (IAM)**, puis sur l’onglet **Attributions de rôles** pour afficher les affectations de rôle pour votre service.

Les membres ayant les rôles suivants peuvent afficher et régénérer les clés : Propriétaire, collaborateur, [contributeur de Search Service](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Pour un accès en fonction de l’identité sur les résultats de recherche, vous pouvez créer des filtres de sécurité pour ajuster les résultats par identité, en supprimant les documents auxquels le demandeur ne doit pas avoir accès. Pour plus d’informations, consultez [Filtres de sécurité](search-security-trimming-for-azure-search.md) et [Sécuriser avec Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Voir aussi

+ [Contrôle d’accès en fonction du rôle dans la Recherche Azure](search-security-rbac.md)
+ [Gestion à l’aide de PowerShell](search-manage-powershell.md) 
+ [Article sur les performances et l’optimisation](search-performance-optimization.md)