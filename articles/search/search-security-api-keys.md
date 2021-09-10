---
title: Authentification par clé API
titleSuffix: Azure Cognitive Search
description: Une clé API contrôle l’accès entrant au point de terminaison de service. Les clés d’administration accordent un accès en écriture. Les clés de requête peuvent être créées pour l’accès en lecture seule.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/25/2021
ms.openlocfilehash: f452aa6ababd338ccc86b7c7c40854367ed46e41
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460621"
---
# <a name="use-api-keys-for-azure-cognitive-search-authentication"></a>Utiliser des clés API pour l’authentification avec Recherche cognitive Azure

Recherche cognitive utilise des clés API comme méthodologie d’authentification principale. Pour les demandes entrantes adressées aux services de recherche, telles que les demandes de création ou d’interrogation d’un index, les clés API sont la seule option d’authentification dont vous disposez. Quelques scénarios de demandes sortantes, en particulier ceux qui impliquent des indexeurs, peuvent utiliser des identités et des rôles Azure Active Directory.

Les clés API sont générées lors de la création du service. La transmission d’une clé API valide à la demande est considérée comme preuve que la demande provient d’un client autorisé. Il y a deux sortes de clés. Les *clés d’administration* fournissent des autorisations en écriture sur le service et accordent également des droits d’interrogation des informations système. Les *clés de requête* communiquent des autorisations de lecture et peuvent être utilisées par les applications pour interroger un index spécifique. 

> [!NOTE]
> L’autorisation pour les opérations de plan de données à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure est désormais en préversion. Vous pouvez utiliser cette fonctionnalité en préversion pour ajouter ou remplacer des clés API [avec des rôles Azure pour la recherche](search-security-rbac.md). 

## <a name="using-api-keys-in-search"></a>Utilisation des clés API dans la recherche

Lors de la connexion à un service de recherche, toutes les requêtes doivent inclure une clé API générée spécialement pour votre service.

+ Dans les [solutions REST](search-get-started-rest.md), la clé API est généralement spécifiée dans un en-tête de requête

+ Dans les [solutions .NET](search-howto-dotnet-sdk.md), une clé est souvent spécifiée comme paramètre de configuration, puis transmise en tant que [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Vous pouvez consulter et gérer les clés API dans le [portail Azure](https://portal.azure.com) ou via [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search) ou [l’API REST](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="page du portail, récupérer les paramètres, section clés" border="false":::

## <a name="what-is-an-api-key"></a>Qu’est-ce qu’une clé API ?

Une clé API est une chaîne unique composée de chiffres et de lettres générés de manière aléatoire, qui sont transmises à chaque requête adressée au service de recherche. Le service acceptera la requête si la requête proprement dite et la clé sont valides. 

Deux types de clés sont utilisés pour accéder à votre service de recherche : administration (lecture-écriture) et requête (lecture seule).

|Clé|Description|limites|  
|---------|-----------------|------------|  
|Admin|Accorde des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données.<br /><br /> Deux clés d’administration, appelées clés *principale* et *secondaire* dans le portail, sont générées quand le service est créé et peuvent être régénérées individuellement à la demande. La possession de deux clés permet de substituer une clé quand l’autre est utilisée pour un accès continu au service.<br /><br /> Les clés d’administration sont spécifiées uniquement dans les en-têtes de requête HTTP. Vous ne pouvez pas insérer de clé API d’administration dans une URL.|2 max. par service|  
|Requête|Accorde un accès en lecture seule aux index et aux documents. Ces clés sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.<br /><br /> Les clés de requête sont créées à la demande.<br /><br /> Les clés de requête peuvent être spécifiées dans un en-tête de requête HTTP pour les opérations de recherche, de suggestion ou de consultation. Vous pouvez également transmettre une clé de requête en tant que paramètre pour une URL. Selon la façon dont votre application cliente formule la demande, il peut être plus facile de transmettre la clé en tant que paramètre de requête :<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 par service|  

 Visuellement, il n’existe aucune distinction entre une clé d’administration et une clé de requête. Les deux clés sont des chaînes composées de 32 caractères alphanumériques générés de façon aléatoire. Si vous n’êtes pas sûr du type de clé spécifié dans votre application, vous pouvez [vérifier les valeurs de clé dans le portail](https://portal.azure.com).  

> [!NOTE]  
> L’insertion de données sensibles comme une `api-key` dans l’URI de requête est considérée comme une pratique peu sécurisée. C’est pourquoi Recherche cognitive Azure accepte uniquement une clé de requête sous forme de `api-key` dans la chaîne de requête, et il est conseillé de procéder autrement, sauf si le contenu de l'index doit être accessible au public. En règle générale, nous vous recommandons de transmettre votre `api-key` en tant qu'en-tête de demande.  

## <a name="find-existing-keys"></a>Rechercher des clés existantes

Vous pouvez obtenir des clés d’accès dans le portail ou via [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search) ou [l’API REST](/rest/api/searchmanagement/).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
1. Sélectionnez le service, puis sur la page de présentation, cliquez sur **Paramètres** >**Clés** pour afficher les clés d'administration et de requête.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="page du portail, afficher les paramètres, section clés" border="false":::

## <a name="create-query-keys"></a>Créer des clés de requête

Les clés de requête sont utilisées pour l'accès en lecture seule aux documents au sein d'un index pour les opérations ciblant une collection de documents. Les requêtes de recherche, de filtrage et de suggestion sont toutes des opérations qui utilisent une clé de requête. Toute opération en lecture seule qui renvoie des données système ou des définitions d'objet, comme une définition d'index ou un statut d'indexation, nécessite une clé d'administration.

Il est essentiel de restreindre l'accès et les opérations dans les applications clientes afin de protéger les ressources de recherche de votre service. Utilisez toujours une clé de requête plutôt qu'une clé d'administration pour toutes les requêtes provenant d'une application cliente.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Répertoriez les [services de recherche](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pour votre abonnement.
3. Sélectionnez le service, puis sur la page de présentation, cliquez sur **Paramètres** >**Clés**.
4. Cliquez sur **Gérer les clés de requête**.
5. Utilisez la clé de requête déjà générée pour votre service, ou créez jusqu'à 50 nouvelles clés de requête. La clé de requête par défaut n'est pas nommée, mais des clés de requête supplémentaires peuvent être nommées pour faciliter la gestion.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Créer ou utiliser une clé de requête" border="false":::

> [!Note]
> Vous trouverez un exemple de code illustrant l'utilisation de la clé de requête dans [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Régénération des clés d’administration

Deux clés d'administration sont créées pour chaque service. Vous pouvez ainsi remplacer la clé primaire par la clé secondaire pour assurer la continuité de vos activités.

1. Dans la page **Paramètres** >**Clés**, copiez la clé secondaire.
2. Pour toutes les applications, mettez à jour les paramètres de la clé API afin d’utiliser la clé secondaire.
3. Régénérez la clé principale.
4. Mettez à jour toutes les applications pour qu’elles utilisent la nouvelle clé principale.

Si, par inadvertance, vous régénérez les deux clés en même temps, toutes les requêtes de client utilisant ces clés échoueront (HTTP 403 Refusé). Toutefois, le contenu ne sera pas supprimé et vous ne subirez pas de verrouillage permanent. 

Vous pouvez toujours accéder au service via le portail ou par programme. Les fonctions de gestion reposent sur un ID d'abonnement et non sur une clé API de service. Elles restent donc disponibles, même si vos clés API ne le sont pas. 

Après avoir créé de nouvelles clés via le portail ou la couche de gestion, l'accès à votre contenu (index, indexeurs, sources de données, cartes de synonymes) est restauré dès que vous disposez des nouvelles clés et que vous les fournissez sur les requêtes.

## <a name="secure-api-keys"></a>Sécuriser les clés API

[Affectations de rôles](search-security-rbac.md) détermine qui peut lire et gérer des clés. Les membres des rôles suivants peuvent afficher et régénérer les clés : Propriétaire, Collaborateur, [Collaborateurs Search Service](../role-based-access-control/built-in-roles.md#search-service-contributor). Le rôle Lecteur n’a pas accès aux clés API.

Les administrateurs des abonnements peuvent afficher et régénérer toutes les clés API. Par précaution, passez en revue les affectations de rôle pour comprendre qui a accès aux clés Admin.

1. Accédez à la page du service de recherche dans le portail Azure.
1. Dans le volet de navigation de gauche, sélectionnez **Contrôle d’accès (IAM)** , puis l’onglet **Attributions de rôles**.
1. Définissez **Étendue** sur **Cette ressource** pour afficher des attributions de rôles pour votre service.

## <a name="see-also"></a>Voir également

+ [Sécurité dans Recherche cognitive Azure](search-security-overview.md)
+ [Contrôle d’accès en fonction du rôle dans Recherche cognitive Azure](search-security-rbac.md)
+ [Gestion à l’aide de PowerShell](search-manage-powershell.md) 