---
title: Résoudre les problèmes courants des indexeurs de recherche - Recherche Azure
description: Résolvez les problèmes courants liés aux indexeurs dans la Recherche Azure, notamment les erreurs de connexion aux sources de données, de pare-feu et de documents manquants.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 4ed18b5f83bdb052f2db6847a320c26a8e49f83e
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147542"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Résoudre les problèmes courants des indexeurs dans la Recherche Azure

Les indexeurs peuvent rencontrer un certain nombre de problèmes lors de l’indexation des données dans la Recherche Azure. Voici les principales catégories de défaillances :

* [Connexion à une source de données](#data-source-connection-errors)
* [Traitement de documents](#document-processing-errors)
* [Ingestion des documents dans un index](#index-errors)

## <a name="data-source-connection-errors"></a>Erreurs de connexion à la source de données

### <a name="blob-storage"></a>Stockage Blob

#### <a name="storage-account-firewall"></a>Pare-feu du compte de stockage

Le Stockage Azure fournit un pare-feu configurable. Par défaut, le pare-feu est désactivé pour que la Recherche Azure puisse se connecter à votre compte de stockage.

Il n’y a pas de message d’erreur spécifique lorsqu’il est activé. En règle générale, les erreurs de pare-feu sont de ce type : `The remote server returned an error: (403) Forbidden`.

Vous pouvez vérifier que le pare-feu est activé sur le [portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). La seule solution de contournement prise en charge consiste à désactiver le pare-feu en choisissant d’autoriser l’accès depuis [« Tous les réseaux »](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Si aucun ensemble de compétences n’est joint à votre indexeur, vous _pouvez_ tenter d’[ajouter une exception](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) pour les adresses IP de votre service de recherche. Toutefois, ce scénario n’est pas pris en charge et n’est pas garanti de fonctionner.

Vous trouverez l’adresse IP de votre service de recherche en effectuant un test ping de son nom de domaine complet (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>L’indexation n’est pas activée

La Recherche Azure comporte une dépendance implicite vis-à-vis de l’indexation Cosmos DB. Si l’indexation automatique est désactivée dans Cosmos DB, la Recherche Azure renvoie un état réussi, mais ne parvient pas à indexer le contenu du conteneur. Pour savoir comment vérifier les paramètres et activer l’indexation, voir [Gérer l’indexation dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal).

## <a name="document-processing-errors"></a>Erreurs de traitement de documents

### <a name="unprocessable-or-unsupported-documents"></a>Documents non exploitables ou non pris en charge

L’indexeur d’objets blob [précise quels formats de documents sont pris en charge explicitement](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Il peut arriver qu’un conteneur de stockage blob contienne des documents non pris en charge ou bien problématiques. Pour ne pas avoir à arrêter votre indexeur sur ces documents, vous pouvez [modifier les options de configuration](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) :

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Contenu de document manquant

L’indexeur d’objets blob [recherche et extrait du texte dans les objets blob d’un conteneur](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). L’extraction de texte peut poser les problèmes suivants :

* Le document ne contient que des images numérisées. Les objets blob PDF comportant du contenu non textuel, comme des images numérisées (JPG), ne produisent pas de résultats dans un pipeline d’indexation blob standard. Si vous avez du contenu d’image comportant des éléments textuels, vous pouvez utiliser la [recherche cognitive](cognitive-search-concept-image-scenarios.md) pour rechercher et extraire le texte.
* L’indexeur d’objets blob est configuré pour indexer uniquement les métadonnées. Pour extraire le contenu, il doit être configuré de façon à [extraire à la fois le contenu et les métadonnées](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) :

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erreurs d’index

### <a name="missing-documents"></a>Documents manquants

Les indexeurs recherchent des documents dans une [source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Parfois, il manque un document qui aurait dû être indexé. Ces erreurs peuvent se produire pour plusieurs raisons :

* Le document n’a pas été indexé. Consultez le portail pour une exécution réussie de l’indexeur.
* Le document a été mis à jour après l’exécution de l’indexeur. Si votre indexeur suit une [planification](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), il s’exécutera à nouveau et trouvera le document.
* La [requête](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) spécifiée dans la source de données exclut le document. Les indexeurs ne peuvent pas indexer de documents qui ne font pas partie de la source de données.
* Des [mappages de champs](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) ou la [recherche cognitive](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) ont modifié le document, qui n’a pas l’aspect prévu.
* Utilisez [l’API Recherche de document](https://docs.microsoft.com/rest/api/searchservice/lookup-document) pour trouver votre document.
