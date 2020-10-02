---
title: Indexeurs accédant à des ressources sécurisées par le biais de points de terminaison privés
titleSuffix: Azure Cognitive Search
description: Guide pratique décrivant la configuration de points de terminaison privés pour permettre à des indexeurs de communiquer avec des ressources sécurisées
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: e9944e95dd452cd00e63280ad8002141591f521e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971435"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>Accéder à des ressources sécurisées par le biais de points de terminaison privés

Les ressources Azure (telles que les comptes de stockage utilisés comme sources de données) peuvent être configurées pour n'être accessibles qu'à partir d'une liste spécifique de réseaux virtuels. Elles peuvent également être configurées de manière à interdire tout accès de type « réseau public ».
Les clients peuvent demander au service Recherche cognitive Azure d'établir une [connexion par point de terminaison privé](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) (sortant) afin d'accéder en toute sécurité aux données de ces sources de données via des indexeurs.

## <a name="shared-private-link-resources-management-apis"></a>API de gestion des ressources de liaison privée partagées

Les points de terminaison privés créés par le service Recherche cognitive Azure à la demande du client pour accéder aux ressources « sécurisées » sont appelés *ressources de liaison privée partagées*. Le client « partage » l'accès à une ressource (telle qu'un compte de stockage) qui a été intégrée au [service Azure Private Link](https://azure.microsoft.com/services/private-link/).

Le service Recherche cognitive Azure offre, via l'API de gestion des recherches, la possibilité de [Créer ou mettre à jour des ressources de liaison privée partagées](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate). Vous utiliserez cette API avec d'autres API de gestion des *ressources de liaison privée partagées* pour configurer l'accès à une ressource sécurisée à partir d'un indexeur Recherche cognitive Azure.

Les connexions par point de terminaison privé à certaines ressources ne peuvent être établies qu'à l'aide de la préversion de l'API de gestion des recherches (`2020-08-01-Preview`), signalée par la mention « préversion » dans le tableau ci-dessous. Les ressources non accompagnées de la mention « préversion » peuvent être créées à la fois via l'API de la préversion et via l'API de la disponibilité générale (`2020-08-01`)

Vous trouverez ci-dessous la liste des ressources Azure vers lesquelles des points de terminaison privés sortants peuvent être créés à partir du service Recherche cognitive Azure. Le `groupId` figurant dans le tableau ci-dessous doit être utilisé tel quel dans l'API (en respectant la casse) pour créer une ressource de liaison privée partagée.

| Ressource Azure | ID de groupe |
| --- | --- |
| Stockage Azure - Blob (ou) ADLS Gen 2 | `blob`|
| Stockage Azure - Tables | `table`|
| Azure Cosmos DB - API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database pour MySQL (préversion) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (préversion) | `sites` |

La liste des ressources Azure pour lesquelles les connexions par point de terminaison privé sortant sont prises en charge peut également être interrogée via l'[API Répertorier les ressources prises en charge](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

Dans le cadre de ce guide, une combinaison d'[ARMClient](https://github.com/projectkudu/ARMClient) et de [Postman](https://www.postman.com/) est utilisée pour illustrer les appels d'API REST.

> [!NOTE]
> Dans l'exemple de ce guide, le service de recherche s'appelle __contoso-search__ et se trouve dans le groupe de ressources __contoso__ d'un abonnement dont l'ID est __00000000-0000-0000 -0000-000000000000__. L'ID de ressource de ce service de recherche est `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

Ce guide explique comment le service __contoso-search__ peut être configuré pour que ses indexeurs aient accès aux données à partir du compte de stockage sécurisé `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Sécuriser votre compte de stockage

Configurez le compte de stockage afin de [n'autoriser l'accès qu'à partir de sous-réseaux spécifiques](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). Via le portail Azure, si vous cochez cette option sans renseigner le reste, cela signifie qu'aucun trafic provenant d'un réseau virtuel n'est autorisé.

   ![Accès au réseau virtuel](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Accès au réseau virtuel")

> [!NOTE]
> L'[approche Service Microsoft approuvé](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) peut être utilisée pour contourner les restrictions liées aux réseaux virtuels ou aux adresses IP sur ce type de compte de stockage, et peut permettre au service de recherche d'accéder aux données du compte de stockage comme décrit dans le [guide pratique](search-indexer-howto-access-trusted-service-exception.md). Toutefois, avec cette approche, la communication entre le service Recherche cognitive Azure et le compte de stockage s'effectue via l'adresse IP publique du compte de stockage, sur le réseau principal sécurisé de Microsoft.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Étape 1 : Créer une ressource de liaison privée partagée avec le compte de stockage

Procédez à l'appel d'API suivant pour demander au service Recherche cognitive Azure d'établir une connexion par point de terminaison privé sortant au compte de stockage

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Le contenu du fichier `create-pe.json` (qui représente le corps de la requête adressée à l'API) est le suivant :

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Une réponse `202 Accepted` est renvoyée en cas de succès ; le processus de création d'un point de terminaison privé sortant est une opération longue (asynchrone). Il implique le déploiement des ressources suivantes :

1. Allocation d'un point de terminaison privé avec une adresse IP privée présentant l'état `"Pending"`. L'adresse IP privée est obtenue à partir de l'espace d'adressage alloué au réseau virtuel de l'environnement d'exécution de l'indexeur privé spécifique au service de recherche. Une fois le point de terminaison privé approuvé, toute communication entre le service Recherche cognitive Azure et le compte de stockage provient de l'adresse IP privée et d'un canal de liaison privé sécurisé.
2. Une zone DNS privée pour le type de ressource, sur la base du `groupId`. Toute recherche DNS sur la ressource privée utilisera ainsi l'adresse IP associée au point de terminaison privé.

Veillez à spécifier le `groupId` qui convient au type de ressource pour lequel vous créez le point de terminaison privé. Toute incompatibilité générera un message d'échec.

Comme toutes les opérations Azure asynchrones, l'appel de `PUT` ​​renvoie une valeur d'en-tête `Azure-AsyncOperation` semblable à la suivante :

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Cet URI peut être régulièrement interrogé pour obtenir l'état de l'opération. Nous vous recommandons d'attendre que l'état de l'opération liée à la ressource de liaison privée partagée ait atteint un état terminal (c'est-à-dire `succeeded`) avant de continuer.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Étape 2a : Approuver la connexion par point de terminaison privé pour le compte de stockage

> [!NOTE]
> Dans cette section, nous utiliserons le portail Azure pour suivre le processus d'approbation d'un point de terminaison privé vers le stockage. L'[API REST](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) disponible via le fournisseur de ressources de stockage peut aussi être utilisée.
>
> D'autres fournisseurs, tels que CosmosDB et Azure SQL Server, proposent également des API de fournisseur de ressources similaires pour gérer les connexions par point de terminaison privé.

Accédez à l'onglet « **Connexions par point de terminaison privé** » du compte de stockage sur le portail Azure. Une demande de connexion par point de terminaison privé doit y figurer, avec le message de demande de l'appel d'API précédent (une fois l'opération asynchrone __réussie__).

   ![Approbation du point de terminaison privé](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Approbation du point de terminaison privé")

Sélectionnez le point de terminaison privé qui a été créé par le service Recherche cognitive Azure (utilisez la colonne « Point de terminaison privé » pour identifier la connexion par point de terminaison privé grâce au nom spécifié dans l'API précédente) et choisissez « Approuver », avec un message approprié (le message n'est pas important). Assurez-vous que la connexion par point de terminaison privé se présente comme suit (1 à 2 minutes peuvent être nécessaires avant que l'état soit mis à jour sur le portail).

![Point de terminaison privé approuvé](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Point de terminaison privé approuvé")

Dès l'approbation de la demande de connexion par point de terminaison privé, le trafic *peut* transiter par le point de terminaison privé. Une fois le point de terminaison privé approuvé, le service Recherche cognitive Azure crée les mappages de zone DNS nécessaires dans la zone DNS créée pour lui.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Étape 2b : Interroger l'état de la ressource de liaison privée partagée

 Pour vérifier que la ressource de liaison privée partagée a bien été mise à jour à l'issue de l'approbation, accédez à son état à l'aide de l'[API GET](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Si la propriété `properties.provisioningState` de la ressource est `Succeeded` tandis que sa propriété `properties.status` est `Approved`, cela signifie que la ressource de liaison privée partagée est fonctionnelle et que les indexeurs peuvent être configurés pour communiquer via le point de terminaison privé.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Étape 3 : Configurer l'indexeur pour qu'il s'exécute dans l'environnement privé

> [!NOTE]
> Cette opération peut être effectuée avant même que la connexion par point de terminaison privé ne soit approuvée. Tant que la connexion par point de terminaison privé n'est pas approuvée, tout indexeur qui tente de communiquer avec une ressource sécurisée (comme le compte de stockage) est en état d'échec temporaire. Il est alors impossible de créer de nouveaux indexeurs. Dès que la connexion par point de terminaison privé est approuvée, les indexeurs ont accès au compte de stockage privé.

1. [Créez une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) qui pointe vers le compte de stockage sécurisé et un conteneur approprié au sein du compte de stockage. L'exemple suivant illustre une demande effectuée via Postman.
![Créer une source de données](media\search-indexer-howto-secure-access\create-ds.png "Création d'une source de données")

2. De même, [créez un index](https://docs.microsoft.com/rest/api/searchservice/create-index) et, si vous le souhaitez, [créez un ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) à l'aide de l'API REST.

3. [Créez un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer) qui pointe vers la source de données, l'index et l'ensemble de compétences créés ci-dessus. En outre, forcez l'exécution de l'indexeur dans l'environnement d'exécution privé en définissant la propriété de configuration de l'indexeur `executionEnvironment` sur `"Private"`.
![Create Indexer](media\search-indexer-howto-secure-access\create-idr.png "Création d'un indexeur")

Une fois créé, l'indexeur indexe le contenu du compte de stockage via la connexion par point de terminaison privé. L'état de l'indexeur peut être surveillé via l'[API d'état de l'indexeur](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Si vous disposez déjà d'indexeurs, vous pouvez simplement les mettre à jour via l'[API PUT](https://docs.microsoft.com/rest/api/searchservice/create-indexer) pour définir `executionEnvironment` sur `"Private"`.

## <a name="troubleshooting-issues"></a>Résolution des problèmes

- Si la création d'un indexeur échoue avec un message d'erreur de type « Les informations d'identification de la source de données ne sont pas valides », cela signifie que la connexion par point de terminaison privé n'a pas été *approuvée* ou qu'elle n'est pas fonctionnelle.
Utilisez l'[API GET](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get) pour accéder à l'état de la ressource de liaison privée partagée. Si elle a été *approuvée*, vérifiez la propriété `properties.provisioningState` de la ressource. Si elle est définie sur `Incomplete`, cela signifie que certaines des dépendances sous-jacentes de la ressource n'ont pas pu être approvisionnées ; réexécutez alors la requête `PUT` pour « recréer » la ressource de liaison privée partagée, ce qui devrait résoudre le problème. Une nouvelle approbation peut être nécessaire ; vérifiez à nouveau l'état de la ressource.
- Si l'indexeur est créé sans que sa propriété `executionEnvironment` ne soit définie, la création peut aboutir, mais l'historique des exécutions de cet indexeur indiquera que celles-ci ont échoué. Vous devez [mettre à jour l'indexeur](https://docs.microsoft.com/rest/api/searchservice/update-indexer) pour spécifier l'environnement d'exécution.
- Si l'indexeur est créé sans que sa propriété `executionEnvironment` ne soit définie et qu'il s'exécute correctement, cela signifie que le service Recherche cognitive Azure a décidé que son environnement d'exécution est l'environnement « privé » spécifique du service de recherche. Celui-ci peut toutefois varier en fonction de différents facteurs (ressources consommées par l'indexeur, charge du service de recherche, etc.) et il peut échouer ultérieurement. Nous vous recommandons vivement de définir `executionEnvironment` sur `"Private"` pour vous assurer que l'environnement d'exécution n'échouera pas.
- Les [quotas et limites](search-limits-quotas-capacity.md) déterminent le nombre de ressources de liaison privée partagée qui peuvent être créées et qui dépendent de la référence SKU du service de recherche.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les points de terminaison privés :

- [Présentation des points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [Configurations DNS nécessaires pour les points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)