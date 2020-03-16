---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: 45cb4e589a00a2ab17fee280bb6066eaee4f24c9
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669195"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Auditez les registres de conteneurs pour lesquels le chiffrement n’est pas activé avec des clés gérées par le client (CMK). Pour plus d’informations sur le chiffrement CMK, consultez : https://aka.ms/acr/CMK. |Audit, Désactivé |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Les registres de conteneurs ne doivent pas autoriser l’accès réseau non restreint](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditez les registres de conteneurs qui n’ont pas de règles réseau (IP ou réseau virtuel) configurées et autorisent tous l’accès réseau par défaut. Les registres de conteneurs avec au moins une règle IP/pare-feu ou un réseau virtuel configuré sont considérés comme conformes. Pour plus d’informations sur les règles de réseau Container Registry, accédez au site suivant : https://aka.ms/acr/vnet. |Audit, Désactivé |1.0.0-preview |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
