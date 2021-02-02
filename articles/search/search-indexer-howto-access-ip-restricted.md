---
title: Autoriser l’accès à des plages d’adresses IP de l’indexeur
titleSuffix: Azure Cognitive Search
description: Configurez les règles de pare-feu IP pour autoriser l’accès aux données par un indexeur Recherche cognitive Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757241"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Configurer des règles de pare-feu IP pour autoriser les connexions d’indexeur (Recherche cognitive Azure)

Les règles de pare-feu IP sur des ressources Azure, telles que les comptes de stockage, les comptes Cosmos DB et les serveurs Azure SQL, autorisent uniquement le trafic provenant de plages d’adresses IP spécifiques pour l’accès aux données.

Cet article décrit comment configurer les règles IP, via le portail Azure, pour un compte de stockage, afin que les indexeurs de Recherche cognitive Azure puissent accéder aux données en toute sécurité. Bien que spécifique au stockage Azure, cette approche fonctionne également pour les autres ressources Azure qui utilisent des règles de pare-feu IP pour sécuriser l’accès aux données.

> [!NOTE]
> Les règles de pare-feu IP pour un compte de stockage ne sont effectives que si le compte de stockage et le service de recherche se trouvent dans des régions différentes. Si votre installation ne le permet pas, nous vous recommandons d’utiliser l’[option d’exception de service approuvé](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obtenir l’adresse IP du service de recherche

Obtenez le nom de domaine complet (FQDN) de votre service de recherche. Il ressemble à ceci : `<search-service-name>.search.windows.net`. Vous pouvez déterminer le FQDN en recherchant votre service de recherche sur le portail Azure.

   ![Obtenir le FQDN du service](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenir le FQDN du service")

Vous pouvez obtenir l’adresse IP du service de recherche en exécutant une commande `nslookup` (ou `ping`) du FQDN. Dans l’exemple ci-dessous, vous devez ajouter « 150.0.0.1 » à une règle de trafic entrant sur le Pare-feu de Stockage Azure. L’accès au compte de stockage Azure peut prendre jusqu’à 15 minutes après la mise à jour des paramètres du Pare-feu.

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

Des adresses IP supplémentaires sont utilisées pour les requêtes qui proviennent de l’[environnement d’exécution mutualisé](search-indexer-securing-resources.md#indexer-execution-environment) de l’indexeur. Vous pouvez récupérer cette plage d’adresses IP à partir de l’étiquette de service.

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

Le moyen le plus simple d’ajouter des plages d’adresses IP à la règle de pare-feu d’un compte de stockage consiste à utiliser le portail Azure. Sur le portail, recherchez le compte de stockage sur et accédez à l’onglet **Pare-feux et réseaux virtuels**.

   ![Pare-feu et réseaux virtuels](media\search-indexer-howto-secure-access\storage-firewall.png "Pare-feu et réseaux virtuels")

Ajoutez les trois adresses IP obtenues précédemment (1 pour l’adresse IP du service de recherche, 2 pour l’étiquette de service `AzureCognitiveSearch`) dans la plage d’adresses, puis cliquez sur **Enregistrer**.

   ![Règles IP de pare-feu](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Règles IP de pare-feu")

La mise à jour des règles de pare-feu prend 5 à 10 minutes après lesquelles les indexeurs accèdent aux données du compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des pare-feu pour le service Stockage Azure](../storage/common/storage-network-security.md)
- [Configurer un pare-feu IP pour Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configurer un pare-feu IP pour Azure SQL Server](../azure-sql/database/firewall-configure.md)