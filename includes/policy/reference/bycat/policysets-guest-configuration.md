---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dd9743f98959f5590198b752c0c2c0037b3d2b28
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046403"
---
|Nom |Description |Stratégies |Version |
|---|---|---|---|
|[Auditer les machines avec des paramètres de sécurité de mot de passe non sécurisés](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Cette initiative permet de déployer les exigences de stratégie et d’auditer les machines avec des paramètres de sécurité de mot de passe non sécurisés. Pour plus d’informations sur les stratégies Guest Configuration, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Déployer les prérequis pour activer les stratégies Guest Configuration sur les machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Cette initiative ajoute une identité managée affectée par le système, et déploie l’extension Guest Configuration appropriée à la plateforme sur les machines virtuelles pouvant être supervisées par des stratégies Guest Configuration. Il s’agit d’un prérequis pour toutes les stratégies Guest Configuration, et il doit être affecté à l’étendue d’attribution des stratégies avant l’utilisation d’une stratégie Guest Configuration. Pour plus d’informations sur Guest Configuration, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0-preview |
|[Les machines Windows doivent répondre aux exigences de la base de référence de sécurité Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Cette initiative audite les machines Windows avec des paramètres qui ne respectent pas la base de référence de sécurité Azure. Pour plus d’informations, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol). |29 |2.0.0-preview |
