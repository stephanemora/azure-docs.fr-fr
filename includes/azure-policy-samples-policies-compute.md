---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170097"
---
|Nom |Description |Effet(s) |Version |
|---|---|---|---|
|[Références SKU de machine virtuelle autorisées](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Cette stratégie vous permet de spécifier un ensemble de références de machine virtuelle que votre organisation peut déployer. |Deny |1.0.0 |
|[Auditer des machines virtuelles pour lesquelles la reprise d’activité après sinistre n’est pas configurée](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Auditez les machines virtuelles configurées sans reprise d’activité. Pour en savoir plus sur la reprise d’activité, consultez https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Faire l’audit des machines virtuelles n’utilisant aucun disque managé](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Cette stratégie fait l’audit des machines virtuelles n’utilisant pas de disque managé |audit |1.0.0 |
|[Déployer l’extension Microsoft IaaSAntimalware par défaut pour Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Cette stratégie déploie une extension Microsoft IaaSAntimalware avec une configuration par défaut quand une machine virtuelle n’est pas configurée avec l’extension de logiciel anti-programme malveillant. |deployIfNotExists |1.0.0 |
|[Les journaux de diagnostic dans les groupes de machines virtuelles identiques doivent être activés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Il est recommandé d’activer les journaux d’activité pour permettre le traçage de l’activité lors des enquêtes requises en cas d’incident ou de compromission. |AuditIfNotExists, Désactivé |1.0.0 |
|[Microsoft Antimalware pour Azure doit être configuré pour mettre à jour automatiquement les signatures de protection](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Cette stratégie permet d’effectuer un audit de toutes les machines virtuelles Windows non configurées avec la mise à jour automatique des signatures de protection de Microsoft Antimalware. |AuditIfNotExists, Désactivé |1.0.0 |
|[L’extension Microsoft IaaSAntimalware doit être déployée sur des serveurs Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Cette stratégie permet d’auditer toute machine virtuelle Windows Server sans extension Microsoft IaaSAntimalware déployée. |AuditIfNotExists, Désactivé |1.0.0 |
|[Seules les extensions de machine virtuelle approuvées doivent être installées](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Cette stratégie régit les extensions de machine virtuelle qui ne sont pas approuvées. |Audit, Refuser, Désactivé |1.0.0 |
|[Exiger la mise à jour corrective automatique de l’image du système d’exploitation sur les groupes de machines virtuelles identiques](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Cette stratégie applique l’activation de la mise à jour corrective automatique des images de système d’exploitation sur les groupes de machines virtuelles identiques afin d’assurer la sécurité des machines virtuelles par l’application sécurisée mensuelle des derniers correctifs de sécurité. |deny |1.0.0 |
|[Les disques non attachés doivent être chiffrés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Cette stratégie permet d’auditer tous les disques non attachés sans chiffrement activé. |Audit, Désactivé |1.0.0 |
|[Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Profitez des améliorations apportées à Azure Resource Manager pour durcir la sécurité de vos machines virtuelles : contrôle d’accès plus puissant (RBAC), audit amélioré, déploiement et gouvernance basés sur ARM, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité |Audit, Refuser, Désactivé |1.0.0 |
