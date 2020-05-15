---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8d390256c44bc37070594596f627fe52f325c6a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82941336"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Cette stratégie permet d’effectuer un audit d’une base de données Azure Database for MariaDB si la sauvegarde géoredondante n’est pas activée. |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Le serveur MariaDB doit utiliser un point de terminaison de service de réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Cette stratégie audite les serveurs MariaDB non configurés pour utiliser un point de terminaison de service de réseau virtuel. Pour plus d’informations, consultez [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork). |AuditIfNotExists, Désactivé |1.0.1 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Le point de terminaison privé doit être activé pour les serveurs MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Cette stratégie audite les serveurs MariaDB non configurés pour utiliser un point de terminaison privé. Pour plus d’informations, consultez [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink). |AuditIfNotExists, Désactivé |1.0.1 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[L’accès au réseau public doit être désactivé pour les serveurs MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Cette stratégie audite les serveurs MariaDB dans votre environnement pour lesquels l’accès au réseau public est activé. Pour plus d’informations, consultez [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542). |Audit, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
