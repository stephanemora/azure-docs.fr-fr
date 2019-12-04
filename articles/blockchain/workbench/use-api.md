---
title: Utilisation des API REST Azure Blockchain Workbench
description: Scénarios d’utilisation de l’API REST d’Azure Blockchain Workbench Preview
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 188bbb9a9f6d289a7950ff74596352dff36e79f2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324200"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Utilisation de l’API REST d’Azure Blockchain Workbench Preview

L’API REST d’Azure Blockchain Workbench Preview permet aux développeurs et aux professionnels de l’information de créer des intégrations riches à des applications blockchain. Ce document vous guide dans l’utilisation de l’API REST Workbench à l’aide de plusieurs méthodes clés. Par exemple, supposons qu’un développeur souhaite créer un client blockchain personnalisé. Ce client blockchain permet aux utilisateurs connectés d’afficher les applications blockchain qui leur sont affectées et d’interagir avec celles-ci. Ce client permet aux utilisateurs d’afficher les instances du contrat et d’intervenir sur les contrats intelligents. Le client utilise l’API REST Workbench dans le contexte de l’utilisateur connecté pour effectuer les actions suivantes :

* Liste des applications
* Répertorier les flux de travail d’une application
* Répertorier les instances de contrat intelligent d’un flux de travail
* Répertorier les actions disponibles d’un contrat
* Exécuter une action pour un contrat

Les exemples d’applications blockchain utilisés dans les scénarios peuvent être [téléchargés sur GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="blockchain-workbench-api-endpoint"></a>Point de terminaison d’API Blockchain Workbench

Les API Blockchain Workbench sont accessibles via un point de terminaison pour votre déploiement. Pour obtenir l’URL du point de terminaison de l’API pour votre déploiement :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.
1. Choisissez le nom du groupe de ressources de votre Blockchain Workbench déployé.
1. Sélectionnez l’en-tête de colonne **TYPE** pour trier la liste par ordre alphabétique et par type.
1. Deux ressources présentent le type **App Service**. Sélectionnez la ressource de type **App Service** *sans* le suffixe « -api ».
1. Dans la section **Vue d’ensemble** App Service, copiez la valeur **URL** qui représente l’URL du point de terminaison de l’API sur votre Blockchain Workbench déployé.

    ![URL du point de terminaison de l'API App Service](media/use-api/app-service-api.png)

## <a name="list-applications"></a>Liste des applications

Une fois qu’un utilisateur s’est connecté au client blockchain, la première tâche consiste à récupérer toutes les applications Blockchain Workbench qui lui sont associées. Dans ce scénario, l’utilisateur a accès à deux applications :

1. [Asset transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md) (Transfert d’actifs)
2. [Refrigerated transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md) (Transport frigorifique)

Utilisez [l’API Applications GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) :

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

La réponse recense toutes les applications blockchain auxquelles un utilisateur a accès dans Blockchain Workbench. Les administrateurs Blockchain Workbench obtiennent toutes les applications blockchain. Les administrateurs non Workbench obtiennent toutes les blockchains pour lesquelles ils ont au moins un rôle d’application associé ou un rôle d’instance de contrat intelligent associé.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Répertorier les flux de travail d’une application

Une fois qu’un utilisateur sélectionne l’application blockchain applicable (par exemple **Asset Transfert**), le client blockchain récupère tous les workflows de l’application blockchain spécifique. Les utilisateurs peuvent alors sélectionner le flux de travail applicable avant de se voir proposer toutes les instances de contrat intelligent pour ce flux. Chaque application blockchain possède un ou plusieurs flux de travail, et chaque flux de travail possède zéro instance de contrat ou des instances de contrat intelligent. Pour une application cliente blockchain comportant un seul workflow, nous recommandons d’éviter le flux d’expérience utilisateur qui permet aux utilisateurs de sélectionner le workflow approprié. Dans ce cas, **Asset Transfer** n’a qu’un seul flux de travail, également appelé **Asset Transfer**.

Utilisez [l’API Applications Workflows GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) :

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

La réponse recense tous les flux de travail de l’application blockchain spécifiée auxquels un utilisateur a accès dans Blockchain Workbench. Les administrateurs Blockchain Workbench obtiennent tous les workflows blockchain. Les administrateurs non Workbench obtiennent tous les workflows pour lesquels ils ont au moins un rôle d’application associé ou un rôle d’instance de contrat intelligent associé.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Répertorier les instances de contrat intelligent d’un flux de travail

Une fois qu’un utilisateur sélectionne le flux de travail applicable (dans ce cas, **Asset Transfer**), le client blockchain récupère toutes les instances de contrat intelligent pour le flux de travail spécifié. Vous pouvez utiliser ces informations pour afficher toutes les instances de contrat intelligent du workflow. Vous pouvez également permettre aux utilisateurs d’explorer en profondeur l’une des instances de contrat intelligent présentées. Dans cet exemple, imaginons qu’un utilisateur souhaite intervenir et ainsi interagir avec l’une des instances de contrat intelligent.

Utilisez [l’API Contracts GET](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) :

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

La réponse recense toutes les instances de contrat intelligent du flux de travail spécifié. Les administrateurs Workbench obtiennent toutes les instances de contrat intelligent. Les administrateurs non Workbench obtiennent toutes les instances de contrat intelligent pour lesquelles ils ont au moins un rôle d’application associé ou un rôle d’instance de contrat intelligent associé.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Répertorier les actions disponibles d’un contrat

Une fois qu’un utilisateur a décidé d’approfondir un contrat, le client blockchain peut lui présenter les actions utilisateur disponibles en fonction de l’état du contrat. Dans cet exemple, l’utilisateur recherche toutes les actions disponibles pour un nouveau contrat intelligent créé :

* Modifier : permet à l’utilisateur de modifier la description et le prix d’une ressource.
* Terminer : permet à l’utilisateur de mettre fin au contrat de la ressource.

Utilisez [l’API Contract Action GET](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) :

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

La réponse recense toutes les actions qu’un utilisateur peut entreprendre étant donné l’état actuel de l’instance de contrat intelligent spécifiée. Les utilisateurs obtiennent toutes les actions applicables si l’utilisateur possède un rôle d’application associé ou est associé à un rôle d’instance de contrat intelligent pour l’état actuel de l’instance de contrat intelligent spécifiée.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Exécuter une action pour un contrat

Un utilisateur peut ensuite décider d’intervenir pour l’instance de contrat intelligent spécifiée. Dans ce cas, imaginons qu’un utilisateur souhaite modifier la description et le prix d’une ressource pour ce qui suit :

* Description : « My updated car »
* Prix : 54321

Utilisez [l’API Contract Action POST](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) :

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

L’utilisateur est uniquement en mesure d’intervenir en fonction de l’état actuel de l’instance de contrat intelligent spécifiée et de son rôle d’application ou de son rôle d’instance de contrat intelligent associé. Si la publication aboutit, une réponse HTTP 200 OK est renvoyée sans corps de réponse.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence de l’API REST Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)