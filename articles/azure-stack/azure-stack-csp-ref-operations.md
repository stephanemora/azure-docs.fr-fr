---
title: Inscrire des locataires pour un suivi de l’utilisation dans Azure Stack | Microsoft Docs
description: Informations sur les opérations de gestion des inscriptions des locataires et sur le suivi de l’utilisation des locataires dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: ce226bb34c5ff8a7ea7dc44d8428da2bb09e25e5
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382380"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Gérer l’inscription des locataires dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article contient des informations sur les opérations que vous pouvez réaliser pour gérer des inscriptions des locataires, et sur le suivi de l’utilisation des locataires dans Azure Stack. Vous pouvez trouver plus d’informations sur comment ajouter, répertorier ou supprimer des mappages de locataires. Vous pouvez utiliser PowerShell ou les points de terminaison de l’API Facturation pour gérer votre suivi d’utilisation.

## <a name="add-tenant-to-registration"></a>Ajouter un locataire à l’inscription

Vous utilisez cette opération lorsque vous voulez ajouter un nouveau locataire à votre inscription, afin que son utilisation soit rapportée sous un abonnement Azure connecté avec son locataire Azure Active Directory (Azure AD).

Vous pouvez aussi utiliser cette opération si vous voulez modifier l’abonnement associé à un locataire. Vous pouvez réexécuter la commande PUT/New-AzureRMResource. L’ancien mappage est remplacé.

Remarque : un seul abonnement Azure peut être associé à un locataire. Si vous essayez d’ajouter un deuxième abonnement à un locataire existant, le premier abonnement est remplacé. 

### <a name="use-api-profiles"></a>Utiliser des profils d’API

Les applets de commande de cet article nécessitent la spécification d’un profil d’API lors de l’exécution de PowerShell. Les profils d’API représentent un ensemble de fournisseurs de ressources Azure et leurs versions d’API. Ils vous aident à utiliser la version appropriée de l’API lors de l’interaction avec plusieurs clouds Azure, par exemple quand vous travaillez avec Azure global et avec Azure Stack. Les profils sont spécifiés par un nom qui correspond à leur date de publication. Avec cet article, vous devez utiliser le profil **2017-09-03**.

Pour plus d’informations sur Azure Stack et les profils d’API, consultez [Gérer les profils de version des API dans Azure Stack](user/azure-stack-version-profiles.md). Pour obtenir des instructions sur l’utilisation et l’exécution avec le profil d’API avec PowerShell, consultez [Utiliser des profils de version des API pour PowerShell dans Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>parameters

| Paramètre                  | Description |
|---                         | --- |
| registrationSubscriptionID | L’abonnement Azure qui était utilisé au moment de l’inscription. |
| customerSubscriptionID     | L’abonnement Azure (pas Azure Stack) appartenant au client à inscrire. Il doit être créé dans l’offre du fournisseur de service cloud (CSP). En pratique, cela veut dire via l’Espace partenaires. Si un client dispose de plus d’un locataire, cet abonnement doit être créé dans le locataire qui sera utilisé pour se connecter à Azure Stack. |
| resourceGroup              | Le groupe de ressources Azure dans lequel est stockée votre inscription. |
| registrationName           | Le nom de l’inscription de votre compte Azure Stack. Il s’agit d’un objet stocké dans Azure. Le nom est en général sous la forme azurestack-CloudID, où CloudID est l’ID du cloud de votre déploiement Azure Stack. |

> [!Note]  
> Les locataires doivent être inscrits avec chaque compte Azure Stack qu’ils utilisent. Si un locataire utilise plus d’un compte Azure Stack, vous devez mettre à jour les inscriptions initiales de chaque déploiement avec l’abonnement du locataire.

### <a name="powershell"></a>PowerShell

Utilisez la cmdlet New-AzureRmResource pour mettre à jour la ressource de l’inscription. Connectez-vous à Azure (`Add-AzureRmAccount`) avec le compte utilisé lors de l’inscription initiale. Voici un exemple d’ajout d’un locataire :

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Appel d’API

**Opération** : PUT  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Réponse** : 201 a été créé  
**Corps de réponse** : Vide  

## <a name="list-all-registered-tenants"></a>Répertorier tous les locataires inscrits

Obtenez une liste de tous les locataires qui ont été ajoutés à une inscription.

 > [!Note]  
 > Si aucun locataire n’a été inscrit, vous ne recevez aucune réponse.

### <a name="parameters"></a>parameters

| Paramètre                  | Description          |
|---                         | ---                  |
| registrationSubscriptionId | L’abonnement Azure qui était utilisé au moment de l’inscription.   |
| resourceGroup              | Le groupe de ressources Azure dans lequel est stockée votre inscription.    |
| registrationName           | Le nom de l’inscription de votre compte Azure Stack. Il s’agit d’un objet stocké dans Azure. Le nom est en général sous la forme **azurestack**-***CloudID***, où ***CloudID*** est l’ID du cloud de votre déploiement Azure Stack.   |

### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande Get-AzureRmResource pour répertorier tous les locataires inscrits. Connectez-vous à Azure (`Add-AzureRmAccount`) avec le compte utilisé lors de l’inscription initiale. Voici un exemple d’ajout d’un locataire :

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Appel d’API

Vous pouvez obtenir une liste de tous les mappages de locataires à l’aide de l’opération GET

**Opération** : GET  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Réponse** : 200  
**Corps de réponse** : 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Supprimer un mappage de locataire

Vous pouvez supprimer un locataire qui a été ajouté à une inscription. Si ce locataire utilise toujours les ressources sur Azure Stack, son utilisation est facturée sur l’abonnement utilisé lors de l’inscription Azure Stack initiale.

### <a name="parameters"></a>parameters

| Paramètre                  | Description          |
|---                         | ---                  |
| registrationSubscriptionId | L’ID d’abonnement pour l’inscription.   |
| resourceGroup              | Le groupe de ressources pour l’inscription.   |
| registrationName           | Le nom de l’inscription.  |
| customerSubscriptionId     | L’ID de l’abonnement client.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Appel d’API

Vous pouvez supprimer des mappages de locataires à l’aide de l’opération DELETE.

**Opération** : DELETE  
**RequestURI** : `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Réponse**: 204, aucun contenu  
**Corps de réponse** : Vide

## <a name="next-steps"></a>Étapes suivantes

 - Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Usage and billing in Azure Stack](azure-stack-billing-and-chargeback.md) (Utilisation et facturation dans Azure Stack).
