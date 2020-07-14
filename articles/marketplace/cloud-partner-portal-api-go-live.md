---
title: API Go Live – Place de marché Azure
description: L’API Go Live lance le processus de création en direct de la liste des offres.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: f356291662851172a3c6917b44891ce901546dfa
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115704"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Les API du portail Cloud Partner sont intégrées à l’Espace partenaires et continueront de fonctionner après la migration de vos offres vers l’Espace partenaires. L’intégration apporte de légères modifications. Passez en revue les modifications répertoriées dans la [référence API du portail Cloud Partner](./cloud-partner-portal-api-overview.md) pour vous assurer que votre code continue à fonctionner après la migration vers l’Espace partenaires.

Cette API démarre le processus de transmission de type push d’une application en production. Cette opération dure généralement longtemps. Cet appel utilise la liste d’e-mails de notification de l’opération de l’API [Publish](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**      |   **Description**                                                           | **Type de données** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificateur du serveur de publication de l’offre à récupérer, par exemple `contoso`       |  String       |
| offerId        | Identificateur de l’offre à récupérer                                   |  String       |
| api-version    | Version la plus récente de l’API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>En-tête
------

|  **Nom**       |     **Valeur**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisation   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exemple de corps

### <a name="response"></a>response

#### <a name="migrated-offers"></a>Offres migrées

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Offres non migrées

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>En-tête de réponse

|  **Nom**             |      **Valeur**                                                            |
|  --------             |      ----------                                                           |
| Emplacement    |  Chemin relatif permettant de récupérer l’état de cette opération            |
|  |  |

### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code** |  **Description**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` : la requête a été acceptée. La réponse contient un emplacement permettant de suivre l’état de l’opération. |
|  400     | `Bad/Malformed request` : le corps de réponse contient des informations d’erreur supplémentaires. |
|  404     |  `Not found` : l’entité spécifiée n’existe pas.                                       |
|  |  |
