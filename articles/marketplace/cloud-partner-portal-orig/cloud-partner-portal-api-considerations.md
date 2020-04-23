---
title: Considérations sur les API | Place de marché Azure
description: Gestion des versions, des erreurs et des problèmes d’autorisation lors de l’utilisation des API de la Place de marché.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256329"
---
# <a name="api-considerations"></a>Considérations sur les API

<a name="api-versioning"></a>Contrôle de version d’API
--------------

> [!NOTE]
> Les API Portail Cloud Partner sont intégrées à l’Espace partenaires et continueront à fonctionner après la migration de vos offres vers l’Espace partenaires. L’intégration apporte de légères modifications. Passez en revue les modifications répertoriées dans la [référence API du portail Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) pour vous assurer que votre code continue à fonctionner après la migration vers l’Espace partenaires.

Plusieurs versions de l’API peuvent être disponibles simultanément. Les clients doivent indiquer la version qu’ils souhaitent invoquer en fournissant le paramètre `api-version` en tant que partie de la chaîne de requête.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Le code HTTP 400 est émis en cas de réponse à une requête avec une version d’API inconnue ou invalide. Cette erreur renvoie la collection des versions connues de l’API dans le corps de la réponse.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Erreurs
------

L’API répond aux erreurs avec les codes d’état HTTP correspondants et renvoie, éventuellement, des informations supplémentaires dans la réponse sérialisée au format JSON.
Lorsque vous recevez une erreur, en particulier une erreur de classe 400, ne réexécutez pas la requête avant d’avoir corrigé la cause sous-jacente. Par exemple, dans l’exemple de réponse ci-dessus, corrigez le paramètre de version de l’API avant de renvoyer la requête.

<a name="authorization-header"></a>En-tête d’autorisation.
--------------------

Pour toutes les API abordées dans cette référence, vous devez transmettre l’en-tête d’autorisation ainsi que le jeton du porteur obtenu auprès d’Azure Active Directory (Azure AD). Cet en-tête est nécessaire pour recevoir une réponse valide ; s’il n’est pas fourni, une erreur `401 Unauthorized` est renvoyée. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
