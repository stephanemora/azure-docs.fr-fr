---
title: Autoriser l’accès à des plages d’adresses IP de l’indexeur
titleSuffix: Azure Cognitive Search
description: Guide décrivant comment configurer des règles de pare-feu IP afin que les indexeurs puissent y accéder.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463238"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Configuration de règles de pare-feu IP pour permettre l’accès de l’indexeur

Les règles de pare-feu IP sur des ressources Azure, telles que des comptes de stockage, des comptes Cosmos DB et des serveurs Azure SQL, autorisent uniquement le trafic provenant de plages d’adresses IP spécifiques pour l’accès aux données.

Cet article décrit comment configurer les règles IP, via le portail Azure, pour un compte de stockage, afin que les indexeurs de Recherche cognitive Azure puissent accéder aux données en toute sécurité. Bien qu’il soit spécifique du stockage, ce guide peut être traduit directement en d’autres ressources Azure qui proposent également des règles de pare-feu IP pour sécuriser l’accès aux données.

> [!NOTE]
> Les règles de pare-feu IP pour un compte de stockage ne sont effectives que si le compte de stockage et le service de recherche se trouvent dans des régions différentes. Si votre installation ne le permet pas, nous vous recommandons d’utiliser l’[option d’exception de service approuvé](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obtenir l’adresse IP du service de recherche

Obtenez le nom de domaine complet (FQDN) de votre service de recherche. Il ressemble à ceci : `<search-service-name>.search.windows.net`. Vous pouvez déterminer le FQDN en recherchant votre service de recherche sur le portail Azure.

   ![Obtenir le FQDN du service](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenir le FQDN du service")

Vous pouvez obtenir l’adresse IP du service de recherche en exécutant une commande `nslookup` (ou `ping`) du FQDN. Il s’agit de l’une des adresses IP à ajouter aux règles de pare-feu.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Obtenir les plages d’adresses IP pour la balise de service « AzureCognitiveSearch »

Vous pouvez obtenir les plages d’adresses IP pour la balise de service `AzureCognitiveSearch` via l’[API de découverte (préversion)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou le [fichier JSON téléchargeable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Pour cette procédure pas à pas, en supposant que le service de recherche est le cloud public Azure, le [fichier JSON public Azure](https://www.microsoft.com/download/details.aspx?id=56519) doit être téléchargé.

   ![Télécharger le fichier JSON](media\search-indexer-howto-secure-access\service-tag.png "Télécharger un fichier JSON")

Dans le fichier JSON, en supposant que le service de recherche se trouve dans la région USA Centre-Ouest, les adresses IP de l’environnement d’exécution de l’indexeur mutualisé suivantes sont répertoriées.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Pour les adresses IP/32, supprimez la chaîne "/32" (52.253.133.74/32 -> 52.253.133.74). Vous pouvez utiliser les autres textuellement.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Ajouter les plages d’adresses IP aux règles de pare-feu IP

Le moyen le plus simple d’ajouter des plages d’adresses IP à la règle de pare-feu d’un compte de stockage consiste à utiliser le portail Azure. Sur le portail, recherchez le compte de stockage sur et accédez à l’onglet «**Pare-feu et réseaux virtuels**».

   ![Pare-feu et réseaux virtuels](media\search-indexer-howto-secure-access\storage-firewall.png "Pare-feu et réseaux virtuels")

Ajoutez les trois adresses IP obtenues précédemment (1 pour l’adresse IP du service de recherche, 2 pour la balise de service `AzureCognitiveSearch`) dans la plage d’adresses, puis cliquez sur « **Enregistrer** ».

   ![Règles IP de pare-feu](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Règles IP de pare-feu")

La mise à jour des règles de pare-feu prend de 5 à 10 minutes après lesquelles les indexeurs peuvent accéder aux données du compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir les deux ensembles d’adresses IP pour autoriser l’accès aux index, utilisez les liens suivants pour mettre à jour les règles de pare-feu IP pour certaines sources de données courantes.

- [Configurer des pare-feu pour le service Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Configurer un pare-feu IP pour CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Configurer un pare-feu IP pour Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)