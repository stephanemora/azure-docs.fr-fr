---
title: API de récupération d’opérations | Place de marché Azure
description: Récupère toutes les opérations sur l’offre ou pour obtenir une opération particulière pour la valeur operationId spécifiée.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 4fc77407ae1c5854d3fe977da5a81f4226bf5305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280471"
---
<a name="retrieve-operations"></a>Récupérer des opérations
===================

Récupère toutes les opérations sur l’offre ou pour obtenir une opération particulière pour la valeur operationId spécifiée. Le client peut utiliser des paramètres de requête pour filtrer les opérations en cours d’exécution.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**          |      **Description**                                                                                           | **Type de données** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identificateur du serveur de publication, par exemple `Contoso`                                                                   |  String       |
|  offerId           |  Identificateur de l’offre                                                                                              |  String       |
|  operationId       |  GUID qui identifie de façon unique l’opération sur l’offre. La valeur operationId peut être récupérée à l’aide de cette API et elle est également retournée dans l’en-tête HTTP de la réponse pour toute opération de longue durée, comme celle de l’API de [publication de l’offre](./cloud-partner-portal-api-publish-offer.md).  |   Guid   |
|  filteredStatus    | Paramètre de requête facultatif utilisé pour filtrer par état (par exemple `running`) sur la collection retournée par cette API.  |   String |
|  api-version       | Dernière version de l’API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>En-tête
------

|  **Nom**          |  **Valeur**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorisation     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemple de corps
------------

### <a name="response"></a>response

#### <a name="get-operations"></a>Opérations GET

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Opération GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Propriétés du corps de réponse

|  **Nom**                    |  **Description**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID qui identifie de façon unique l’opération                                                       |
|  submissionType              | Identifie le type d’opération signalé pour l’offre, par exemple `Publish/GGoLive`      |
|  createdDateTime             | Date/heure UTC de création de l’opération                                                       |
|  lastActionDateTime          | Date/heure UTC de la dernière mise à jour de l’opération                                       |
|  status                      | État de l’opération, parmi `not started` \| `running` \| `failed` \| `completed`. Une seule opération à la fois peut avoir l’état `running`. |
|  error                       | Message d’erreur pour l’échec des opérations                                                               |
|  |  |


### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code**  |   **Description**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` : la demande a été traitée et les opérations sont annulées de manière synchrone.        |
|  400      | `Bad/Malformed request` : le corps de la réponse d’erreur peut contenir plus d’informations.                    |
|  403      | `Forbidden` : le client n’a pas accès à l’espace de noms spécifié.                          |
|  404      | `Not found` : l’entité spécifiée n’existe pas.                                                 |
|  |  |
