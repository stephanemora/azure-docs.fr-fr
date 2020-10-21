---
title: Codes d’erreur courants pour Azure Key Vault | Microsoft Docs
description: Codes d’erreur courants pour Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876780"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Codes d’erreur courants pour Azure Key Vault

Les codes d’erreur répertoriés dans le tableau suivant peuvent être renvoyés par une opération sur un coffre de clés Azure.

| Code d'erreur | Message utilisateur |
|--|--|
| VaultAlreadyExists |  Le coffre de clés spécifié existe déjà (dans un état de suppression réversible ou dans un autre abonnement). |
| VaultNameNotValid |  Le nom du coffre doit comporter 24 caractères alphanumériques et commencer par une lettre. |
| AccessDenied |  Il se peut qu’il vous manque des autorisations dans la stratégie d’accès pour effectuer cette opération. |
| ForbiddenByFirewall |  L’adresse du client n’est pas autorisée et l’appelant n’est pas un service approuvé. |
| ConflictError |  Vous demandez plusieurs opérations sur le même élément.  |
| RegionNotSupported |  La région Azure spécifiée n’est pas prise en charge pour cette ressource. |
| SkuNotSupported |  Le type de SKU spécifié n’est pas pris en charge pour cette ressource. |
| ResourceNotFound |  La ressource Azure spécifiée est introuvable. |
| CertificateExpired |  Vérifiez la date d’expiration et la période de validité du certificat. |


## <a name="next-steps"></a>Étapes suivantes

- Consulter le [Guide du développeur Azure Key Vault](developers-guide.md)
- En savoir plus sur [l’authentification auprès du coffre de clés](authentication.md)
