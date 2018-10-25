---
title: Go Live| Microsoft Docs
description: L’API Go Live lance le processus de création en direct de la liste des offres.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c7d643f0c7885e64636a107d22ce332b1ba9371c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806054"
---
<a name="go-live"></a>Go Live
=======

Cette API démarre le processus de transmission de type push d’une application en production. Cette opération dure généralement longtemps. Cet appel utilise la liste d’e-mails de notification de l’opération de l’API [Publish](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**      |   **Description**                                                           | **Type de données** |
|  --------      |   ---------------                                                           | ------------- |
| publisherid    | Identificateur du serveur de publication de l’offre à récupérer, par exemple `contoso`       |  Chaîne       |
| offerId        | Identificateur de l’offre à récupérer                                   |  Chaîne       |
| api-version    | Version la plus récente de l’API                                                   |  Date         |
|  |  |  |


<a name="header"></a>En-tête
------

|  **Nom**       |     **Valeur**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Authorization   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exemple de corps
------------

### <a name="response"></a>response

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>En-tête de réponse

|  **Nom**             |      **Valeur**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  URL à interroger pour déterminer l’état actuel de l’opération.            |
|  |  |


### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code** |  ** Description**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` : la requête a été acceptée. La réponse contient un emplacement permettant de suivre l’état de l’opération. |
|  400     | `Bad/Malformed request` : le corps de réponse contient des informations d’erreur supplémentaires. |
|  404     |  `Not found` : l’entité spécifiée n’existe pas.                                       |
|  |  |
