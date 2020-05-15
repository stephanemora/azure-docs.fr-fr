---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d7759dbf1377cb46a9868dbcf01c61e20a7a2144
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82941176"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Gérer les types de clés de certificat autorisés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |Cette stratégie gère les types de clés autorisés pour les certificats. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[Gérer les noms de courbe autorisés pour les certificats de chiffrement à courbe elliptique](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |Cette stratégie gère les noms de courbe elliptique autorisés pour les certificats de chiffrement à courbe elliptique. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[Gérer les déclencheurs d’action de durée de vie de certificat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |Cette stratégie gère la configuration des déclencheurs d’action de durée de vie de certificat avant l’expiration du certificat. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[Gérer la période de validité de certificat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |Cette stratégie gère la période de validité maximale des certificats en mois. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[Gère les certificats émis par une autorité de certification non intégrée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |Cette stratégie gère les certificats émis par une autorité de certification non intégrée spécifiée. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[Gérer les certificat émis par une autorité de certification intégrée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |Cette stratégie gère les certificats émis par une autorité de certification spécifiée intégrée au coffre de clés. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[Gérer les certificats arrivés à un nombre de jours spécifiés avant expiration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |Cette stratégie gère les certificats arrivés à un nombre de jours spécifié de leur date d’expiration. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[Gérer la taille minimale de clé pour les certificats RSA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |Cette stratégie gère la taille de clé minimale des certificats RSA. |audit, deny, disabled |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
