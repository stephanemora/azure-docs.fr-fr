---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 81783becd727eb1aef415f539ddb10a5a124fd70
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367563"
---
|Nom |Description |Effet(s) |Version |Source |
|---|---|---|---|
|[Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Auditez les registres de conteneurs pour lesquels le chiffrement n’est pas activé avec des clés gérées par le client (CMK). Pour plus d’informations sur le chiffrement CMK, consultez : https://aka.ms/acr/CMK. |Audit, Désactivé |1.0.0-preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Les registres de conteneurs ne doivent pas autoriser l’accès réseau non restreint](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditez les registres de conteneurs qui n’ont pas de règles réseau (IP ou réseau virtuel) configurées et autorisent tous l’accès réseau par défaut. Les registres de conteneurs avec au moins une règle IP/pare-feu ou un réseau virtuel configuré sont considérés comme conformes. Pour plus d’informations sur les règles de réseau Container Registry, accédez au site suivant : https://aka.ms/acr/vnet. |Audit, Désactivé |1.0.0-preview |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
