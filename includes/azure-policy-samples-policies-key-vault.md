---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170127"
---
|Name |Description |Effet(s) |Version |
|---|---|---|---|
|[Déployer les paramètres de diagnostic de Key Vault sur Event Hub](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Déploie les paramètres de diagnostic de Key Vault pour les envoyer en streaming dans un hub d’événements régional sur tout coffre de clés nouveau ou mis à jour pour lequel les paramètres de diagnostic sont manquants. |deployIfNotExists |1.0.0 |
|[Les journaux de diagnostic dans Key Vault doivent être activés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Auditer l’activation des journaux de diagnostic. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau |AuditIfNotExists, Désactivé |1.0.0 |
|[Les objets Key Vault doivent être récupérables](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Cette stratégie vérifie si les objets de coffre de clés ne sont pas récupérables. La fonctionnalité de suppression réversible permet de conserver les ressources pendant une période de conservation donnée (90 jours), même après une opération de SUPPRESSION, tout en donnant l’impression que l’objet est supprimé. Quand « Protection de purge » est activée, un coffre ou un objet à l’état supprimé ne peut pas être vidé avant la fin de la période de conservation de 90 jours. Ces coffres et objets peuvent être récupérés, ce qui garantit aux clients le suivi de la stratégie de conservation. |Audit, Désactivé |1.0.0 |
