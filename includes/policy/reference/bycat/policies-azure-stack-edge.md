---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c68188ec1ee0c0e3a8d174bcb8c19d8a450c22dc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498647"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les appareils Azure Stack Edge doivent utiliser le chiffrement double](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Pour sécuriser les données au repos sur l’appareil, vérifiez qu’elles sont chiffrées doublement, que l’accès aux données est contrôlé et une fois que l’appareil est désactivé, que les données sont effacées de façon sécurisée des disques de données. Le chiffrement double correspond à l’utilisation de deux couches de chiffrement : Chiffrement BitLocker XTS-AES 256 bits sur les volumes de données et chiffrement intégré des disques durs. Pour plus d’informations, consultez la documentation de présentation de la sécurité de l’appareil Azure Stack Edge. |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
