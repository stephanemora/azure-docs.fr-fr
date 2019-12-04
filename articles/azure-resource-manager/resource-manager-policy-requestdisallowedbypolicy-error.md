---
title: Erreur RequestDisallowedByPolicy
description: Décrit la cause de l’erreur RequestDisallowedByPolicy lors du déploiement de ressources avec Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ed4008c6c6705f307f8c21bd43992523701a4ee6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150521"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure

Cet article décrit la cause de l’erreur RequestDisallowedByPolicy. Il fournit également des solutions pour la résoudre.

## <a name="symptom"></a>Symptôme

Au cours du déploiement, il se peut que vous receviez une erreur **RequestDisallowedByPolicy** vous empêchant de créer les ressources. L’exemple ci-après illustre l’erreur :

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Résolution de problèmes

Pour extraire des informations sur la stratégie qui a bloqué votre déploiement, utilisez l’une des méthodes suivantes :

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans PowerShell, fournissez cet identificateur de stratégie en tant que paramètre `Id` pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>D’Azure CLI

Dans Azure CLI, spécifiez le nom de la définition de stratégie :

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solution

Pour des raisons de sécurité ou de conformité, les administrateurs de votre abonnement ont la possibilité d’affecter des stratégies qui limitent la façon dont les ressources sont déployées. Par exemple, votre abonnement peut être soumis à une stratégie qui empêche la création d’adresses IP publiques, de groupes de sécurité réseau, d’itinéraires définis par l’utilisateur ou de tables d’itinéraires. Le message d’erreur de la section **Symptômes** indique le nom de la stratégie.
Pour résoudre ce problème, passez en revue les stratégies liées aux ressources et déterminez comment déployer des ressources conformes.

Pour plus d’informations, consultez les articles suivants :

- [Présentation d’Azure Policy](../governance/policy/overview.md)
- [Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)
