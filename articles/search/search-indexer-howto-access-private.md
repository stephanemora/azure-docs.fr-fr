---
title: Connexions d’indexeurs via un point de terminaison privé
titleSuffix: Azure Cognitive Search
description: Configurez les connexions de l’indexeur pour accéder au contenu à partir d’autres ressources Azure protégées par un point de terminaison privé.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 79bb517faffdda7e9d7ddef45e7b52f5e81dc201
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589679"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Établir des connexions d’indexeurs via un point de terminaison privé

> [!NOTE]
> Vous pouvez utiliser l’[approche Service Microsoft approuvé](../storage/common/storage-network-security.md#trusted-microsoft-services) pour contourner les restrictions de réseau virtuel ou d’adresse IP sur un compte de stockage. Vous pouvez également autoriser le service de recherche à accéder aux données dans le compte de stockage. Pour ce faire, consultez [Accès de l’indexeur au stockage Azure avec l’exception de service approuvé](search-indexer-howto-access-trusted-service-exception.md).
> 
> Toutefois, avec cette approche, la communication entre le service Recherche cognitive Azure et votre compte de stockage s’effectue via l’adresse IP publique du compte de stockage, sur le réseau principal sécurisé de Microsoft.

De nombreuses ressources Azure, telles que les comptes de stockage Azure, peuvent être configurées pour accepter les connexions à partir d’une liste de réseaux virtuels et refuser les connexions externes qui proviennent d’un réseau public. Si vous utilisez un indexeur pour indexer des données dans Recherche cognitive Azure et que votre source de données se trouve sur un réseau privé, vous pouvez créer une [connexion de point de terminaison privée](../private-link/private-endpoint-overview.md) sortante pour atteindre les données.

Cette méthode de connexion de l’indexeur est soumise aux deux conditions suivantes :

+ La ressource Azure qui fournit le contenu ou le code doit être inscrite au préalable auprès du [service de liaison privée Azure](https://azure.microsoft.com/services/private-link/).

+ Le service de Recherche cognitive Azure doit avoir le niveau de service De base ou supérieur. Cette fonctionnalité n’est pas disponible sur le niveau gratuit. En outre, si votre indexeur dispose d’un ensemble de compétences, le niveau doit être Standard 2 (S2) ou supérieur. Pour plus d’informations, consultez [Limites du Service](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>API de gestion des ressources de liaison privée partagées

Les points de terminaison privés des ressources privées créées par les API Recherche cognitive Azure sont appelés *ressources de liaison privée partagées*. En effet, vous « partagez » l’accès à une ressource, un compte de stockage par exemple, qui a été intégrée au [service Azure Private Link](https://azure.microsoft.com/services/private-link/).

Grâce à son API REST de gestion, Recherche cognitive Azure propose l’opération [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) que vous pouvez utiliser pour configurer l’accès à partir d’un indexeur Recherche cognitive Azure.

Vous pouvez créer des connexions de point de terminaison privé à certaines ressources uniquement à l’aide de la préversion de l’API de gestion de Recherche (version *2020-08-01-Preview* ou ultérieure), qui est désignée *préversion* dans le tableau suivant. Les ressources ne portant pas la mention *préversion* peuvent être créées soit depuis la préversion, soit depuis la version de l’API mise à la disposition générale (*2020-08-01* ou version ultérieure).

Le tableau suivant dresse la liste des ressources Azure pour lesquelles des points de terminaison privés sortants peuvent être créés à partir du service Recherche cognitive Azure. Pour créer une ressource de liaison privée partagée, entrez les valeurs d’**ID de groupe** exactement telles qu’elles sont écrites dans l’API. Les valeurs respectent la casse.

| Ressource Azure | ID de groupe |
| --- | --- |
| Stockage Azure - Blob | `blob`|
| Azure Storage - Data Lake Storage Gen2 | `dfs` et `blob` |
| Stockage Azure - Tables | `table`|
| Azure Cosmos DB - API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database pour MySQL (préversion) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (préversion) | `sites` |

Vous pouvez également envoyer une requête à propos des ressources Azure pour lesquelles les connexions par point de terminaison privé sortant sont prises en charge en utilisant [la liste des API prises en charge](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported).

Dans le reste de cet article, une combinaison du portail Azure (ou d’[Azure CLI](/cli/azure/) si vous préférez) et de [Postman](https://www.postman.com/) (ou tout autre client HTTP, comme [curl](https://curl.se/), si vous préférez) est utilisée pour illustrer les appels de l’API REST.

> [!NOTE]
> Pour créer une connexion de point de terminaison privé à Azure Data Lake Storage Gen2, vous devez créer deux points de terminaison privés. Un point d'extrémité privé avec le groupID 'dfs' et un autre point d'extrémité privé avec le groupID 'blob'.

## <a name="set-up-indexer-connection-through-private-endpoint"></a>Configurer la connexion de l’indexeur via un point de terminaison privé

Utilisez les instructions suivantes pour configurer une connexion d’indexeur via un point de terminaison privé vers une ressource Azure sécurisée.

Les exemples de cet article se basent sur les hypothèses suivantes :
* le service de recherche s’appelle _contoso-search_ et se trouve dans le groupe de ressources _contoso_ d’un abonnement dont l’ID est _00000000-0000-0000-0000-000000000000_. 
* L’ID de ressource de ce service de recherche est _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_.

### <a name="step-1-secure-your-azure-resource"></a>Étape 1 : Sécuriser votre ressource Azure

Les étapes de restriction de l’accès varient en fonction des ressources. Les scénarios suivants illustrent trois des types de ressources les plus courants.

- Scénario 1 : Source de données

    L’exemple suivant illustre la configuration d’un compte Stockage Azure. Si vous sélectionnez cette option sans renseigner le reste, cela signifie qu’aucun trafic provenant d’un réseau virtuel n’est autorisé.

    ![Capture d’écran du volet « Pare-feux et réseaux virtuels » pour Stockage Azure mettant en avant l’option permettant d’autoriser l’accès aux réseaux sélectionnés.](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- Scénario 2 : Azure Key Vault

    L’exemple suivant illustre la configuration d’Azure Key Vault.
 
    ![Capture d’écran du volet « Pare-feux et réseaux virtuels » pour Azure Key Vault mettant en avant l’option permettant d’autoriser l’accès aux réseaux sélectionnés.](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- Scénario 3 : Azure Functions

    Aucune modification de paramètre réseau n’est nécessaire pour Azure Functions. Dans les étapes suivantes, lorsque vous créerez le point de terminaison privé partagé, la fonction autorisera automatiquement l’accès via un lien privé après la création d’un point de terminaison privé partagé à la fonction.

### <a name="step-2-create-a-shared-private-link-resource-to-the-azure-resource"></a>Étape 2 : Créer une ressource de liaison privée partagée vers la ressource Azure

La section suivante explique comment créer une ressource de liaison privée partagée à l’aide du portail Azure ou d’Azure CLI.

#### <a name="option-1-portal"></a>Option 1 : Portail

> [!NOTE]
> Le portail prend uniquement en charge la création d’un point de terminaison privé partagé à l’aide de valeurs d’ID de groupe en disponibilité générale. Pour MySQL et Azure Functions, utilisez la procédure Azure CLI décrite dans l’option 2, qui suit.

Pour demander à Recherche cognitive Azure de créer une connexion de point de terminaison privé sortant, accédez au volet Accès privé partagé et cliquez sur « Ajouter un accès privé partagé ». Dans le volet qui s’ouvre à droite, vous pouvez choisir « Se connecter à une ressource Azure dans mon annuaire » ou « Se connecter à une ressource Azure par ID ou alias de ressource ».

Lorsque vous utilisez la première option (recommandé), le volet vous aide à sélectionner la ressource Azure appropriée et à renseigner automatiquement d’autres propriétés telles que l’ID de groupe de la ressource et le type de ressource.

   ![Capture d’écran du volet « Ajouter un accès privé partagé » montrant une expérience guidée pour la création d’une ressource de liaison privée partagée. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

Lorsque vous utilisez la deuxième option, vous pouvez entrer manuellement l’ID de ressource Azure et choisir l’ID de groupe approprié. Les ID de groupe sont répertoriés au début de cet article.

![Capture d’écran du volet « Ajouter un accès privé partagé » montrant l’expérience manuelle pour la création d’une ressource de liaison privée partagée. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### <a name="option-2-azure-cli"></a>Option 2 : Azure CLI

Vous pouvez également effectuer l’appel d’API suivant avec [Azure CLI](/cli/azure/) : Utilisez la version d’API 2020-08-01-preview si vous utilisez un ID de groupe en préversion. Par exemple, les ID de groupe *sites* et *mysqlServer* sont en préversion et implique que vous utilisiez l’API en préversion.

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

L’exemple suivant illustre le contenu du fichier *create-pe.json* :

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

Une réponse `202 Accepted` est retournée en cas de réussite. Le processus de création d’un point de terminaison privé sortant est une opération longue (asynchrone). Il implique le déploiement des ressources suivantes :

+ un point de terminaison privé avec une adresse IP privée présentant l’état `"Pending"`. L’adresse IP privée est obtenue à partir de l’espace d’adressage alloué au réseau virtuel de l’environnement d’exécution de l’indexeur privé spécifique au service de recherche. Une fois le point de terminaison privé approuvé, toute communication entre le service Recherche cognitive Azure et la ressource Azure provient de l'adresse IP privée et d'un canal de liaison privé sécurisé.

+ Une zone DNS privée pour le type de ressource, sur la base du `groupId`. En déployant cette ressource, vous êtes assuré que toute recherche DNS sur la ressource privée utilisera l’adresse IP associée au point de terminaison privé.

Veillez à spécifier le `groupId` qui convient au type de ressource pour lequel vous créez le point de terminaison privé. Toute incompatibilité générera un message d'échec.

### <a name="step-3-check-the-status-of-the-private-endpoint-creation"></a>Étape 3 : Vérifier l’état de la création du point de terminaison privé

Au cours de cette étape, vous allez vérifier que l’état d'approvisionnement de la ressource bascule sur « Réussi ».

#### <a name="option-1-portal"></a>Option 1 : Portail

> [!NOTE]
> L’état d’approvisionnement est visible dans le portail pour la disponibilité générale et les ID de groupe en préversion.

Le portail vous indique l’état du point de terminaison privé partagé. Dans l’exemple suivant, l’état est « Mis à jour ».

![Capture d’écran du volet « Ajouter un accès privé partagé », montrant la création de la ressource en cours. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

Une fois la ressource créée, vous recevrez une notification du portail et l’état de l’approvisionnement de la ressource passera à « Réussi ».

![Capture d’écran du volet « Ajouter un accès privé partagé », montrant la création de la ressource terminée. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### <a name="option-2-azure-cli"></a>Option 2 : Azure CLI

L'appel `PUT` pour créer le point de terminaison privé partagé retourne une valeur d’en-tête `Azure-AsyncOperation` qui se présente comme suit :

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Vous pouvez interroger l’état en interrogeant manuellement la valeur `Azure-AsyncOperationHeader`.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### <a name="step-4-approve-the-private-endpoint-connection"></a>Étape 4 : Approuver la connexion de point de terminaison privé

> [!NOTE]
> Dans cette section, vous utilisez le portail Azure pour suivre le processus d’approbation d’un point de terminaison privé vers la ressource Azure à laquelle vous vous connectez. Vous pouvez également utiliser l’[API REST](/rest/api/storagerp/privateendpointconnections) disponible via le fournisseur de ressources de stockage.
>
> D’autres fournisseurs, tels qu’Azure Cosmos DB ou Azure SQL Server, offrent des API de fournisseur de ressources de stockage similaires pour la gestion des connexions de point de terminaison privé.

1. Dans le portail Azure, accédez à la ressource Azure à laquelle vous vous connectez, puis sélectionnez l’onglet **Mise en réseau**. Accédez ensuite à la section qui répertorie les connexions de point de terminaison privé. L’exemple suivant illustre un compte de stockage. Une fois l’opération asynchrone réussie, une requête de connexion par point de terminaison privé doit y figurer, avec le message de demande d’appel d’API précédent.

   ![Capture d’écran du portail Azure, affichant le volet « Connexions de point de terminaison privé ».](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Sélectionnez le point de terminaison privé créé par Recherche cognitive Azure. Dans la colonne **point de terminaison privé**, identifiez la connexion de point de terminaison privé par le nom spécifié dans l’API précédente, sélectionnez **Approuver**, puis entrez un message approprié. Le contenu du message n’a pas d’importance.

   Assurez-vous que la connexion de points de terminaison privés s’affiche comme indiqué dans la capture d’écran suivante. La mise à jour de l’état dans le portail peut prendre une à deux minutes.

   ![Capture d’écran du portail Azure, affichant l’état « Approuvé » dans le volet « Connexions de point de terminaison privé ».](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Dès l’approbation de la requête de connexion de points de terminaison privés, le trafic *peut* transiter par le point de terminaison privé. Une fois le point de terminaison privé approuvé, le service Recherche cognitive Azure crée les mappages de zone DNS nécessaires dans la zone DNS créée pour cela.

### <a name="step-5-query-the-status-of-the-shared-private-link-resource"></a>Étape 5 : Interroger l'état de la ressource de liaison privée partagée

Pour confirmer que la ressource de liaison privée partagée a été mise à jour après l’approbation, revenez au panneau « Accès privé partagé » du service de recherche sur le portail Azure et vérifiez l’« État de la connexion ».

   ![Capture d’écran du portail Azure, montrant une ressource de liaison privée partagée « Approuvée ».](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

Vous pouvez également obtenir l’« État de la connexion » à l’aide de l’[API GET](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get).

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

Elle retourne du JSON, où l’état de la connexion s’affiche sous la forme « status » sous la section « properties ». L’exemple suivant illustre un compte de stockage.

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

Si la propriété Provisioning State (`properties.provisioningState`) de la ressource est `Succeeded` tandis que sa propriété Connection State (`properties.status`) est `Approved`, cela signifie que la ressource de liaison privée partagée est fonctionnelle et que l’indexeur peut être configuré pour communiquer via le point de terminaison privé.

### <a name="step-6-configure-the-indexer-to-run-in-the-private-environment"></a>Étape 6 : Configurer l’indexeur pour qu’il s’exécute dans l’environnement privé

> [!NOTE]
> Vous pouvez effectuer cette opération avant même que la connexion de points de terminaison privés ne soit approuvée. Tant que la connexion de points de terminaison privés n’est pas approuvée, tout indexeur qui tente de communiquer avec une ressource sécurisée (par exemple, le compte de stockage) est en état d’échec temporaire. Il est alors impossible de créer de nouveaux indexeurs. Dès que la connexion de points de terminaison privés est approuvée, les indexeurs ont accès au compte de stockage privé.

Les étapes suivantes montrent comment configurer l’indexeur pour qu’il s’exécute dans l’environnement privé à l’aide de l’API REST. Vous pouvez également définir l’environnement d’exécution à l’aide de l’éditeur JSON dans le portail.

1. Créez la définition de la source de données, l’index et l’ensemble de compétences (le cas échéant) comme vous le feriez normalement. Aucune propriété relative à ces définitions ne varie lors de l’utilisation d’un point de terminaison privé partagé.

1. [Créez un indexeur](/rest/api/searchservice/create-indexer) qui pointe vers la source de données, l’index et l’ensemble de compétences que vous avez créés à l’étape précédente. En outre, forcez l’exécution de l’indexeur dans l’environnement d’exécution privé en définissant la propriété de configuration de l’indexeur `executionEnvironment` sur `private`.

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    L’exemple suivant illustre la requête dans Postman.
    
    ![Capture d’écran montrant la création d’un indexeur sur l’interface utilisateur de Postman.](media\search-indexer-howto-secure-access\create-indexer.png)    

Une fois l’indexeur créé avec succès, il doit se connecter à la ressource Azure via la connexion de point de terminaison privé. Vous pouvez surveiller l’état de l’indexeur à l’aide de l’[API d’état de l’indexeur](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Si vous disposez déjà d’indexeurs, vous pouvez les mettre à jour via l’[API PUT](/rest/api/searchservice/create-indexer) en configurant `executionEnvironment` sur `private` ou à l’aide de l’éditeur JSON dans le portail.

## <a name="troubleshooting"></a>Dépannage

+ Si la création de votre indexeur échoue avec un message d’erreur de type « Les informations d’identification de la source de données ne sont pas valides », cela signifie que la connexion de point de terminaison privé n’a pas été *approuvée* ou qu’elle n’est pas fonctionnelle. Pour résoudre ce problème : 
  + Utiliser l’[API GET](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) pour accéder à l’état de la ressource de liaison privée partagée. Si l’état est *approuvé*, vérifiez la propriété `properties.provisioningState` de la ressource. Si l’état est `Incomplete`, cela signifie que certaines des dépendances sous-jacentes de la ressource n’ont pas pu être configurées. La réémission de la requête `PUT` pour recréer la ressource de liaison privée partagée doit résoudre le problème. Une réapprobation peut être nécessaire. Vérifiez à nouveau l’état de la ressource pour vous assurer que le problème est résolu.

+ Si vous créez un indexeur sans que sa propriété `executionEnvironment` ne soit définie, la création peut être un succès, mais l’historique des exécutions de cet indexeur indiquera que celles-ci ont échoué. Pour résoudre ce problème :
  + [Mettre à jour l’indexeur](/rest/api/searchservice/update-indexer) pour spécifier l’environnement d’exécution.

+ Si vous avez créé indexeur sans que sa propriété `executionEnvironment` ne soit définie et qu’il s’exécute correctement, cela signifie que le service Recherche cognitive Azure a décidé que son environnement d’exécution est l’environnement *privé* spécifique au service de recherche. Cela peut varier en fonction des ressources consommées par l’indexeur, de la charge sur le service de recherche et d’autres facteurs. L’échec peut survenir ultérieurement. Pour résoudre ce problème :
  + Nous vous recommandons vivement de définir la propriété `executionEnvironment` sur `private` pour vous assurer qu’elle n’échouera pas à l’avenir.

+ Si vous consultez la page réseau de votre source de données sur le portail Azure et que vous sélectionnez un point de terminaison privé que vous avez créé pour votre service Recherche cognitive Azure afin d'accéder à cette source de données, vous risquez de rencontrer l'erreur *Aucun accès*. Ceci est normal. Vous pouvez modifier l’état de la demande de connexion via la page du portail du service cible, mais pour continuer à gérer la ressource de liaison privée partagée, vous devez accéder à celle-ci sur la page réseau de votre service de recherche sur le portail Azure.

Les [quotas et limites](search-limits-quotas-capacity.md) déterminent le nombre de ressources de liaison privée partagée qui peuvent être créées et qui dépendent de la référence SKU du service de recherche.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les points de terminaison privés :

+ [Résoudre les problèmes avec les ressources Private Link partagées](troubleshoot-shared-private-link-resources.md)
+ [Présentation des points de terminaison privés](../private-link/private-endpoint-overview.md)
+ [Configurations DNS nécessaires pour les points de terminaison privés](../private-link/private-endpoint-dns.md)