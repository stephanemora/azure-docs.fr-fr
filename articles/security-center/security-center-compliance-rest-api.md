---
title: Supervision de la conformité Azure Security Center avec l’API REST | Microsoft Docs
description: Examinez les résultats des analyses de conformité automatisées à l’aide de l’API REST Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 3f07928897df01f5d654fa6a6bffce14290b24e4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295308"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Examiner les résultats de conformité de Security Center à l’aide des API REST Azure

Dans cet article, vous allez découvrir comment récupérer les informations de conformité de la sécurité pour obtenir la liste des abonnements à l’aide des API REST Azure.

## <a name="review-compliance-for-each-subscription"></a>Examiner la conformité pour chaque abonnement

L’exemple ci-dessous obtient des informations d’évaluation de la sécurité pour une conformité et un abonnement donnés à l’aide de l’opération d’[obtention de conformités](/rest/api/securitycenter/compliances/get).

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

Le paramètre `{complianceName}` est obligatoire et doit contenir le nom de la conformité évaluée pour `{subscription-id}`. La sortie contient les résultats de l’évaluation, par exemple :

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Examiner la conformité pour plusieurs abonnements

Pour obtenir les données pour plusieurs abonnements, effectuez l’appel ci-dessous pour d’abord obtenir la liste des abonnements à l’aide de l’opération d’[obtention de la liste d’abonnements](/rest/api/resources/subscriptions/list). Appelez ensuite la méthode d’[obtention de confidentialités](/rest/api/securitycenter/compliances/get) ci-dessus pour chacun des ID d’abonnement retournés.

L’appel d’API est :

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

ce qui retourne un tableau de valeurs comme les suivantes. Utilisez les valeurs subscriptionId dans l’appel ci-dessus pour examiner les informations de conformité pour tous les abonnements.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






