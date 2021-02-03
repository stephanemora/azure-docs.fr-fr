---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b631f447a5c43fb1695b6e07ec387c83747a8613
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220351"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Utilisez des clés gérées par le client pour gérer le chiffrement au repos du contenu de vos registres. Par défaut, les données sont chiffrées au repos avec des clés gérées par le service, mais les clés gérées par le client (CMK) sont généralement requises pour respecter les normes de conformité réglementaires. Les clés CMK permettent de chiffrer les données avec une clé Azure Key Vault que vous avez créée et dont vous êtes le propriétaire. Vous avez le contrôle total et la responsabilité du cycle de vie des clés, notamment leur permutation et leur gestion. Pour en savoir plus sur le chiffrement CMK, consultez [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Les registres de conteneurs ne doivent pas autoriser un accès réseau non restreint](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Par défaut, les registres de conteneurs Azure acceptent les connexions via Internet à partir des hôtes sur n’importe quel réseau. Pour protéger vos registres contre les menaces potentielles, autorisez l’accès uniquement à partir d’adresses IP publiques ou de plages d’adresses spécifiques. Si votre registre n’a pas de règle IP/de pare-feu ou de réseau virtuel configuré, il apparaîtra dans les ressources non saines. Apprenez-en plus sur les règles réseau de Container Registry ici : [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) et ici [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Les registres de conteneurs doivent utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link vous permet de connecter votre réseau virtuel aux services Azure sans adresse IP publique au niveau de la source ou de la destination. La plateforme Private Link gère la connectivité entre le consommateur et les services sur le réseau principal Azure. En mappant des points de terminaison privés à vos registres de conteneurs au lieu de l’ensemble du service, vous êtes également protégé contre les risques de fuite de données. En savoir plus : [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
