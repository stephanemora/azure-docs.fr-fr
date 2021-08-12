---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c4af0de20797830e6e558743cee92fca1f33bf4
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114388306"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les appareils Azure Stack Edge doivent utiliser le chiffrement double](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Pour sécuriser les données au repos sur l’appareil, vérifiez qu’elles sont chiffrées doublement, que l’accès aux données est contrôlé et une fois que l’appareil est désactivé, que les données sont effacées de façon sécurisée des disques de données. Le chiffrement double correspond à l’utilisation de deux couches de chiffrement : Chiffrement BitLocker XTS-AES 256 bits sur les volumes de données et chiffrement intégré des disques durs. Pour plus d’informations, consultez la documentation de présentation de la sécurité de l’appareil Azure Stack Edge. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
