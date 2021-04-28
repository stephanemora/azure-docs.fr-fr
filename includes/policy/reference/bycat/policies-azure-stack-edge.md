---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1913d07957adf71761e6fe65cb5bc9247f44219d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866483"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les appareils Azure Stack Edge doivent utiliser le chiffrement double](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Pour sécuriser les données au repos sur l’appareil, vérifiez qu’elles sont chiffrées doublement, que l’accès aux données est contrôlé et une fois que l’appareil est désactivé, que les données sont effacées de façon sécurisée des disques de données. Le chiffrement double correspond à l’utilisation de deux couches de chiffrement : Chiffrement BitLocker XTS-AES 256 bits sur les volumes de données et chiffrement intégré des disques durs. Pour plus d’informations, consultez la documentation de présentation de la sécurité de l’appareil Azure Stack Edge. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
