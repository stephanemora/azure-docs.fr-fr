---
title: Résolution des erreurs courantes
description: Découvrez comment résoudre les problèmes liés à l’interrogation des ressources Azure avec Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480465"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Résoudre les erreurs à l’aide d’Azure Resource Graph

Vous pouvez rencontrer des erreurs lors de l’interrogation des ressources Azure avec Azure Resource Graph. Cet article décrit différentes erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="finding-error-details"></a>Recherche des détails de l’erreur

La plupart des erreurs sont le résultat d’un problème lors de l’exécution d’une requête avec Azure Resource Graph. En cas d’échec d’une requête, le kit de développement logiciel (SDK) fournit des détails sur la requête qui a échoué. Ces informations précisent le problème afin que vous puissiez le résoudre et que la requête suivante réussisse.

## <a name="general-errors"></a>Erreurs générales.

### <a name="toomanysubscription"></a>Scénario : Trop d’abonnements

#### <a name="issue"></a>Problème

Les clients ayant accès à plus de 1000 abonnements, y compris les abonnements inter-clients avec [Azure Lighthouse](../../../lighthouse/overview.md), ne peuvent pas extraire les données de tous les abonnements dans un seul appel à Azure Resource Graph.

#### <a name="cause"></a>Cause :

Azure CLI et PowerShell transfèrent uniquement les 1000 premiers abonnements à Azure Resource Graph. L’API REST pour Azure Resource Graph accepte un nombre maximal d’abonnements pour exécuter la requête.

#### <a name="resolution"></a>Résolution :

Requêtes par lots pour la requête avec un sous-ensemble d’abonnements à conserver sous la limite d’abonnement de 1000. La solution utilise le paramètre d’**Abonnement** dans PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
- Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
- Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.