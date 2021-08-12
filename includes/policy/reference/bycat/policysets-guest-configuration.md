---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 06f337cb524b1ca158baf0627ccdfa88c4ae4e40
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387872"
---
|Nom |Description |Stratégies |Version |
|---|---|---|---|
|[Auditer les machines avec des paramètres de sécurité de mot de passe non sécurisés](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Cette initiative permet de déployer les exigences de stratégie et d’auditer les machines avec des paramètres de sécurité de mot de passe non sécurisés. Pour plus d’informations sur les stratégies Guest Configuration, consultez [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |9 |1.0.0 |
|[Déployer les prérequis pour activer les stratégies Guest Configuration sur les machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Cette initiative ajoute une identité managée affectée par le système, et déploie l’extension Guest Configuration appropriée à la plateforme sur les machines virtuelles pouvant être supervisées par des stratégies Guest Configuration. Il s’agit d’un prérequis pour toutes les stratégies Guest Configuration, et il doit être affecté à l’étendue d’attribution des stratégies avant l’utilisation d’une stratégie Guest Configuration. Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md). |4 |1.0.0 |
|[\[Préversion\] : Les machines Windows doivent répondre aux impératifs de référence liés à la sécurité d’Azure Compute](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Cette initiative audite les machines Windows dont les paramètres ne correspondent pas à la base de référence de sécurité pour Azure Compute. Pour plus d’informations, consultez [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md). |29 |2.0.1-preview |