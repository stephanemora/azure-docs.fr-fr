---
title: Passer en revue les données de facturation d’un Accord de Mise en Œuvre Entreprise avec l’API REST | Microsoft Docs
description: Découvrez comment utiliser les API REST Azure pour passer en revue les informations de facturation de l’Accord de Mise en Œuvre Entreprise.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064399"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Passer en revue la facturation de l’Accord de Mise en Œuvre Entreprise avec les API REST

Les API de génération de rapports Azure vous aident à passer en revue et à gérer vos coûts Azure.

Vous allez découvrir ici comment récupérer la facture actuelle associée à une inscription de compte d’entreprise.

Pour récupérer la facture actuelle :
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Générer la demande  

Le paramètre `{enrollmentID}` est obligatoire et doit contenir l’ID d’inscription pour le compte d’entreprise.

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur une `Bearer` [clé d’API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) valide. |  

Cet exemple montre un appel synchrone qui retourne des informations détaillées pour le cycle de facturation actuel. Pour des raisons de performances, les appels synchrones retournent des informations pour le mois dernier.  Vous pouvez également appeler [l’API de façon asynchrone](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) pour retourner des données pour 36 mois.


## <a name="response"></a>response  

Le code d’état 200 (OK) est retourné pour une réponse correcte, qui contient une liste de coûts détaillés pour votre compte.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Chaque élément dans **data** représente un coût :

|Propriété de la réponse|Description|
|----------------|----------|
|**cost** | Montant facturé, dans une devise appropriée pour l’emplacement du centre de données. |
|**subscriptionGuid** | ID global unique pour l’abonnement. | 
|**departmentId** | ID du service, le cas échéant. |
|**date** | Date à laquelle le coût a été facturé. |
|**balises** | Chaîne JSON contenant les étiquettes associées à l’abonnement. |
|**resourceGroup**|Nom du groupe de ressources contenant l’objet qui a généré le coût. |
|**nextLink**| Quand elle est définie, spécifie une URL pour la « page » suivante d’informations détaillées. Vide quand la page est la dernière. |  
||
  
Les ID de service, les groupes de ressources, les étiquettes et les champs associés sont définis par l’administrateur du compte d’entreprise.  

Cet exemple est abrégé ; consultez [Obtenir les détails d’utilisation](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) pour une description complète de chaque champ de la réponse. 

Les autres codes d’état indiquent les conditions d’erreur. Dans ces cas, l’objet de réponse explique pourquoi la demande a échoué.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Étapes suivantes 
- Consulter [Présentation des rapports d’entreprise](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Examiner [l’API REST de facturation d’entreprise](https://docs.microsoft.com/rest/api/billing/)   
- [Bien démarrer avec l’API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
