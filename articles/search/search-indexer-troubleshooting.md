---
title: Résoudre les problèmes courants des indexeurs de recherche
titleSuffix: Azure Cognitive Search
description: Résolvez les problèmes courants liés aux indexeurs dans la Recherche cognitive Azure, notamment les erreurs de connexion aux sources de données, de pare-feu et de documents manquants.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740055"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Résoudre les problèmes courants des indexeurs dans la Recherche cognitive Azure

Les indexeurs peuvent rencontrer un certain nombre de problèmes lors de l’indexation des données dans la Recherche cognitive Azure. Voici les principales catégories de défaillances :

* [Connexion à une source de données ou à d’autres ressources](#connection-errors)
* [Traitement de documents](#document-processing-errors)
* [Ingestion des documents dans un index](#index-errors)

## <a name="connection-errors"></a>Erreurs de connexion

> [!NOTE]
> Les indexeurs ont une prise en charge limitée pour accéder aux sources de données et à d’autres ressources sécurisées par les mécanismes de sécurité réseau Azure. Actuellement, les indexeurs peuvent accéder uniquement aux sources de données par le biais de mécanismes de restriction de plage d’adresses IP ou de règles NSG correspondantes, le cas échéant. Vous trouverez ci-dessous des détails sur l’accès à chaque source de données prise en charge.
>
> Vous trouverez l’adresse IP de votre service Search en effectuant un test ping de son nom de domaine complet (p. ex., `<your-search-service-name>.search.windows.net`).
>
> Vous pouvez trouver la plage d’adresses IP de `AzureCognitiveSearch` [l’étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) en utilisant des [fichiers JSON téléchargeables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou via [l’API de détection d’étiquettes de service](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). La plage d’adresses IP est mise à jour chaque semaine.

### <a name="configure-firewall-rules"></a>Configurer les règles de pare-feu

Stockage Azure, CosmosDB et Azure SQL fournissent un pare-feu configurable. Il n’y a pas de message d’erreur spécifique lorsqu’il est activé. En règle générale, les erreurs de pare-feu sont génériques et se présentent comme `The remote server returned an error: (403) Forbidden` ou `Credentials provided in the connection string are invalid or have expired`.

Vous pouvez utiliser deux options pour autoriser les indexeurs à accéder à ces ressources dans une telle instance :

* Désactiver le pare-feu en choisissant d’autoriser l’accès de **Tous les réseaux** (si possible).
* Vous pouvez également autoriser l’accès à l’adresse IP de votre service Search et à la plage d’adresses IP de `AzureCognitiveSearch` [l’étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) dans les règles de pare-feu de votre ressource (restriction de plage d’adresses IP).

Pour plus d’informations sur la configuration des restrictions de plage d’adresses IP pour chaque type de source de données, consultez les liens suivants :

* [Stockage Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitation** : Comme indiqué dans la documentation ci-dessus pour le stockage Azure, les restrictions de plage d’adresses IP ne fonctionnent que si votre service Search et votre compte de stockage se trouvent dans des régions différentes.

Azure Functions (qui peut être utilisé comme une [compétence API web personnalisée](cognitive-search-custom-skill-web-api.md)) prend également en charge les [restrictions d’adresse IP](../azure-functions/ip-addresses.md#ip-address-restrictions). La liste d’adresses IP à configurer correspond à l’adresse IP de votre service Search et à la plage d’adresses IP de `AzureCognitiveSearch` l’étiquette de service.

Pour plus d’informations sur l’accès aux données dans SQL Server sur une machine virtuelle Azure, cliquez [ici](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurer les règles du groupe de sécurité réseau (NSG)

Lors de l’accès aux données d’une instance gérée SQL, ou lorsqu’une machine virtuelle Azure est utilisée comme URI de service we pour une [compétence API web personnalisée](cognitive-search-custom-skill-web-api.md), les clients n’ont pas à se préoccuper des adresses IP spécifiques.

Dans ce cas, la machine virtuelle Azure ou l’instance gérée SQL peut être configurée pour résider dans un réseau virtuel. Ensuite, le groupe de sécurité réseau peut être configuré pour filtrer le type de trafic réseau pouvant circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel.

`AzureCognitiveSearch` L’étiquette de service peut être utilisée directement dans les [règles de groupe de sécurité réseau](../virtual-network/manage-network-security-group.md#work-with-security-rules) entrantes sans avoir besoin de rechercher sa plage d’adresses IP.

Pour plus d’informations sur l’accès aux données dans une instance managée SQL, cliquez [ici](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>« L’indexation » CosmosDB n’est pas activée

La Recherche cognitive Azure comporte une dépendance implicite vis-à-vis de l’indexation Cosmos DB. Si l’indexation automatique est désactivée dans Cosmos DB, la Recherche cognitive Azure renvoie un état réussi, mais ne parvient pas à indexer le contenu du conteneur. Pour savoir comment vérifier les paramètres et activer l’indexation, voir [Gérer l’indexation dans Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Stratégies d’accès conditionnel SharePoint Online

Lors de la création d’un indexeur SharePoint Online, vous allez effectuer une étape qui vous oblige à vous connecter à votre application AAD après avoir fourni un code d’appareil. Si vous recevez un message indiquant que la connexion a réussi, mais que votre administrateur exige que l’appareil demandant l’accès soit managé, il est probable que l’indexeur ne peut pas accéder à la bibliothèque de documents SharePoint Online en raison d’une stratégie d’[accès conditionnel](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview).

Pour mettre à jour la stratégie afin d’autoriser l’indexeur à accéder à la bibliothèque de documents, suivez les étapes ci-dessous :

1. Ouvrez le Portail Azure et recherchez **Accès conditionnel Azure AD**, puis sélectionnez **Stratégies** dans le menu de gauche. Si vous n’avez pas accès à cette page, vous devez trouver une personne disposant d’un accès ou demander un accès.

1. Déterminez quelle stratégie empêche l’indexeur SharePoint Online d’accéder à la bibliothèque de documents. La stratégie susceptible de bloquer l’indexeur inclura le compte d’utilisateur que vous avez utilisé pour l’authentification lors de l’étape de création de l’indexeur dans la section **Utilisateurs et groupes**. La stratégie peut également avoir des **conditions** qui :
    * Limitent les plateformes **Windows**.
    * Limitent les **Applications mobiles et clients de bureau**.
    * Ont l’**État de l’appareil** configuré sur **Oui**.

1. Une fois que vous avez confirmé qu’une stratégie bloque l’indexeur, vous devez ensuite créer une exemption pour l’indexeur. Récupérez l’adresse IP du service de recherche.

    1. Obtenez le nom de domaine complet (FQDN) de votre service de recherche. Il ressemble à ceci : `<search-service-name>.search.windows.net`. Vous pouvez déterminer le FQDN en recherchant votre service de recherche sur le portail Azure.

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

1. Récupérez les plages d’adresses IP pour l’environnement d’exécution de l’indexeur pour votre région.

    Des adresses IP supplémentaires sont utilisées pour les requêtes qui proviennent de l’[environnement d’exécution mutualisé](search-indexer-securing-resources.md#indexer-execution-environment) de l’indexeur. Vous pouvez récupérer cette plage d’adresses IP à partir de l’étiquette de service.

    Vous pouvez obtenir les plages d’adresses IP pour la balise de service `AzureCognitiveSearch` via l’[API de découverte (préversion)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou le [fichier JSON téléchargeable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Pour cette procédure pas à pas, en supposant que le service de recherche est le cloud public Azure, le [fichier JSON public Azure](https://www.microsoft.com/download/details.aspx?id=56519) doit être téléchargé.

   ![Télécharger le fichier JSON](media\search-indexer-troubleshooting\service-tag.png "Télécharger un fichier JSON")

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

1. Retournez sur la page Accès conditionnel dans le Portail Azure, sélectionnez **Emplacements nommés** dans le menu de gauche, puis choisissez **Emplacement des plages d’adresses IP +** . Donnez un nom à votre nouvel emplacement nommé et ajoutez les plages d’adresses IP pour votre service de recherche et les environnements d’exécution de l’indexeur que vous avez collectés au cours des deux dernières étapes.
    * Pour l’adresse IP de votre service de recherche, vous devrez peut-être ajouter « /32 » à la fin de l’adresse IP, car le système n’accepte que des plages d’adresses IP valides.
    * N’oubliez pas que pour les plages d’adresses IP de l’environnement d’exécution de l’indexeur, vous devez uniquement ajouter les plages d’adresses IP pour la région dans laquelle se trouve votre service de recherche.

1. Excluez le nouvel emplacement nommé de la stratégie. 
    1. Sélectionnez **Stratégies** dans le menu de gauche. 
    1. Sélectionnez la stratégie qui bloque l’indexeur.
    1. Sélectionnez **Conditions**.
    1. Sélectionner **Emplacements**.
    1. Sélectionnez **Exclure**, puis ajoutez le nouvel emplacement nommé.
    1. **Enregistrez** les modifications.

1. Attendez quelques minutes pour que la stratégie se mette à jour et applique les nouvelles règles de stratégie.

1. Tentative de recréation de l’indexeur
    1. Envoyez une requête de mise à jour pour l’objet source de données que vous avez créé.
    1. Renvoyez la requête de création de l’indexeur. Utilisez le nouveau code pour vous connecter, puis envoyez une autre requête de création d’indexeur après la réussite de la connexion.

## <a name="document-processing-errors"></a>Erreurs de traitement de documents

### <a name="unprocessable-or-unsupported-documents"></a>Documents non exploitables ou non pris en charge

L’indexeur d’objets blob [précise quels formats de documents sont pris en charge explicitement](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Il peut arriver qu’un conteneur de stockage blob contienne des documents non pris en charge ou bien problématiques. Pour ne pas avoir à arrêter votre indexeur sur ces documents, vous pouvez [modifier les options de configuration](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) :

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
* L’indexeur d’objets blob est configuré pour indexer uniquement les métadonnées. Pour extraire le contenu, il doit être configuré de façon à [extraire à la fois le contenu et les métadonnées](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex) :

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erreurs d’index

### <a name="missing-documents"></a>Documents manquants

Les indexeurs recherchent des documents dans une [source de données](/rest/api/searchservice/create-data-source). Parfois, il manque un document qui aurait dû être indexé. Ces erreurs peuvent se produire pour plusieurs raisons :

* Le document n’a pas été indexé. Consultez le portail pour une exécution réussie de l’indexeur.
* Vérifiez votre valeur de [suivi des modifications](/rest/api/searchservice/create-data-source#data-change-detection-policies). Si la valeur de la limite supérieure est une date future, tous les documents dont la date est antérieure à celle-ci sont ignorés par l’indexeur. Vous pouvez comprendre l’état de suivi des modifications de votre indexeur à l’aide des champs « initialTrackingState » et « finalTrackingState » dans l’[état de l’indexeur](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Le document a été mis à jour après l’exécution de l’indexeur. Si votre indexeur suit une [planification](/rest/api/searchservice/create-indexer#indexer-schedule), il s’exécutera à nouveau et trouvera le document.
* La [requête](/rest/api/searchservice/create-data-source) spécifiée dans la source de données exclut le document. Les indexeurs ne peuvent pas indexer de documents qui ne font pas partie de la source de données.
* Des [mappages de champs](/rest/api/searchservice/create-indexer#fieldmappings) ou l’[enrichissement de l’IA](./cognitive-search-concept-intro.md) ont modifié le document, qui n’a pas l’aspect prévu.
* Utilisez [l’API Recherche de document](/rest/api/searchservice/lookup-document) pour trouver votre document.