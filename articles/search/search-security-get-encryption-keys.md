---
title: Récupérer les informations sur la clé de chiffrement
titleSuffix: Azure Cognitive Search
description: Récupérez le nom et la version de la clé de chiffrement utilisés dans un mappage d’index ou de synonymes afin de pouvoir gérer la clé dans Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003815"
---
# <a name="find-encrypted-objects-and-information"></a>Rechercher des objets et des informations chiffrés

Dans Azure Cognitive Search, les clés de chiffrement gérées par le client sont créées, stockées et gérées dans Azure Key Vault. Si vous avez besoin de déterminer si un objet est chiffré ou si le nom ou la version de la clé a été utilisée dans Azure Key Vault, utilisez l’API REST ou un kit de développement logiciel (SDK) Azure pour récupérer la propriété **encryptionKey** à partir d’une définition d’objet dans votre service de recherche.

Les objets qui ne sont pas chiffrés avec une clé gérée par le client auront une propriété **encryptionkey** vide. Dans le cas contraire, vous pouvez voir une définition similaire à l’exemple suivant.

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

La construction **encryptionkey** est la même pour tous les objets chiffrés. Il s’agit d’une propriété de premier niveau, sur le même niveau que le nom et la description de l’objet.

## <a name="get-the-admin-api-key"></a>Obtient la clé de l’API administrateur

Avant de pouvoir récupérer des définitions d’objets à partir d’un service de recherche, vous devez fournir une clé d’API administrateur. Des clés d’API administrateur sont requises sur les requêtes qui interrogent les définitions d’objets et les métadonnées. Le moyen le plus simple d’accéder à la clé d’API d’administration se fait via le portail.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez la page de présentation de votre service de recherche.

1. Sur le côté gauche, cliquez sur **Clés** et copiez une API d’administration. Une clé d’administrateur est requise pour l’index et la récupération de carte des synonymes.

Pour les étapes restantes, basculez vers PowerShell et l’API REST. Le portail n’affiche pas d’informations de clé de chiffrement pour un objet.

## <a name="retrieve-object-properties"></a>Récupérer les propriétés de l'objet

Utilisez PowerShell et REST pour exécuter les commandes suivantes afin de configurer les variables et récupérer les définitions d’objets. 

Sinon, vous pouvez également utiliser les kits de développement logiciel (SDK) Azure pour [.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes), [Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient), [JavaScript](/javascript/api/@azure/search-documents/searchindexclient) et [Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex).

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons d’[activer la journalisation](../key-vault/general/logging.md) sur Azure Key Vault afin de pouvoir analyser l’utilisation de la clé.

Pour plus d’informations sur l’utilisation de la clé Azure ou sur la configuration du chiffrement managé par le client :

+ [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide de PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Configurer des clés gérées par le client pour le chiffrement des données dans le service Recherche cognitive Azure](search-security-manage-encryption-keys.md)
