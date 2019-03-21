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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594676"
---
<a name="go-live"></a>Go Live
=======

Cette API démarre le processus de transmission de type push d’une application en production. Cette opération dure généralement longtemps. Cet appel utilise la liste d’e-mails de notification de l’opération de l’API [Publish](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**      |   **Description**                                                           | **Type de données** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificateur du serveur de publication de l’offre à récupérer, par exemple `contoso`       |  Chaîne       |
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

| **Code** |  **Description**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` : la requête a été acceptée. La réponse contient un emplacement permettant de suivre l’état de l’opération. |
|  400     | `Bad/Malformed request` : le corps de réponse contient des informations d’erreur supplémentaires. |
|  404     |  `Not found` : l’entité spécifiée n’existe pas.                                       |
|  |  |
