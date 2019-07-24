---
title: Limitation des requêtes - Azure Resource Manager
description: Décrit comment utiliser la limitation avec des requêtes Azure Resource Manager lorsque les limites d’abonnement ont été atteintes.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b382b9ae35d492b4c779b8f7ee360fb378d54e08
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399714"
---
# <a name="throttling-resource-manager-requests"></a>Limitation des requêtes de Resource Manager

Pour chaque abonnement et locataire Azure, Resource Manager autorise jusqu’à 12 000 demandes de lecture et 1 200 demandes d’écriture par heure. Ces limites sont définies d’après l’ID du principal qui effectue les requêtes et l’ID d’abonnement ou l’ID du locataire. Si vos demandes proviennent de plusieurs ID de principal, votre limite dans l’abonnement ou le locataire est supérieure à 12 000 et 1 200 par heure.

Les requêtes sont appliquées à votre abonnement ou à votre locataire. Les requêtes appliquées à l’abonnement sont celles qui impliquent la transmission de votre ID d’abonnement, par exemple pour récupérer les groupes de ressources dans votre abonnement. Les requêtes appliquées au locataire n’incluent pas votre ID d’abonnement, notamment pour la récupération des emplacements Azure valides.

Ces limites s’appliquent à chaque instance Azure Resource Manager. Chaque région Azure comporte plusieurs instances, et Azure Resource Manager est déployé dans toutes les régions Azure.  Par conséquent, dans la pratique, les limites sont effectivement beaucoup plus importantes que celles-ci, car les requêtes utilisateur sont généralement prises en charge par de nombreuses instances différentes.

Si votre application ou script atteint ces limites, vous devez limiter vos requêtes. Cet article vous montre comment déterminer les requêtes restantes dont vous disposez avant d’atteindre la limite et comment réagir si vous avez atteint la limite.

Lorsque vous atteignez la limite, vous recevez le code d’état HTTP **429 Trop de requêtes**.

Azure Resource Graph limite le nombre de requêtes à ses opérations. Les étapes décrites dans cet article pour déterminer les requêtes restantes et comment réagir si la limite est atteinte s’appliquent également à Resource Graph. Toutefois, Resource Graph définit ses propres limites et son propre taux de réinitialisation. Pour plus d’informations, consultez [Throttle in Azure Resource Graph (Limitation dans Azure Resource Graph)](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Requêtes restantes
Vous pouvez déterminer le nombre de requêtes restantes en examinant les en-têtes de réponse. Les requêtes de lecture retournent une valeur dans l’en-tête pour le nombre de requêtes de lecture restantes. Les requêtes d’écriture incluent une valeur pour le nombre de requêtes d’écriture restantes. Le tableau suivant décrit les en-têtes de réponse que vous pouvez examiner pour ces valeurs :

| En-tête de réponse | Description |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Requêtes de lecture restantes étendues à l’abonnement. Cette valeur est renvoyée pour les opérations de lecture. |
| x-ms-ratelimit-remaining-subscription-writes |Requêtes d’écriture restantes étendues à l’abonnement. Cette valeur est renvoyée pour les opérations d’écriture. |
| x-ms-ratelimit-remaining-tenant-reads |Requêtes de lecture restantes étendues au locataire |
| x-ms-ratelimit-remaining-tenant-writes |Requêtes d’écriture restantes étendues au locataire |
| x-ms-ratelimit-remaining-subscription-resource-requests |Requêtes de type de ressource restantes étendues à l’abonnement.<br /><br />Cette valeur d’en-tête est retournée uniquement si un service a remplacé la limite par défaut. Resource Manager ajoute cette valeur au lieu des requêtes de lecture ou d’écriture de l’abonnement. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Requêtes de collecte de type de ressource restantes étendues à l’abonnement.<br /><br />Cette valeur d’en-tête est retournée uniquement si un service a remplacé la limite par défaut. Cette valeur fournit le nombre de requêtes de collecte restantes (répertorier les ressources). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Requêtes de type de ressource restantes étendues au locataire.<br /><br />Cet en-tête est ajouté uniquement pour les requêtes au niveau du locataire, et uniquement si un service a remplacé la limite par défaut. Resource Manager ajoute cette valeur au lieu des requêtes de lecture ou d’écriture du locataire. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Requêtes de collecte de type de ressource restantes étendues au locataire.<br /><br />Cet en-tête est ajouté uniquement pour les requêtes au niveau du locataire, et uniquement si un service a remplacé la limite par défaut. |

## <a name="retrieving-the-header-values"></a>Récupération des valeurs d’en-tête
La récupération de ces valeurs d’en-tête dans votre code ou script est similaire à la récupération de n’importe quelle valeur d’en-tête. 

Par exemple, en **C#** , vous récupérez la valeur d’en-tête d’un objet **HttpWebResponse** nommé **response** avec le code suivant :

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

Dans **PowerShell**, vous récupérez la valeur de l’en-tête d’une opération Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Pour obtenir un exemple PowerShell complet, consultez [Vérifier les limites de Resource Manager pour un abonnement](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Si voulez voir les requêtes restantes pour le débogage, vous pouvez définir le paramètre **-Debug** sur votre cmdlet **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Retourne un grand nombre de valeurs, notamment la valeur de réponse suivante :

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Pour obtenir des limites d’écriture, utilisez une opération d’écriture : 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Renvoie un grand nombre de valeurs, notamment celles-ci :

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Dans **l’interface de ligne de commande**, vous récupérez la valeur d’en-tête à l’aide de l’option plus détaillée.

```azurecli
az group list --verbose --debug
```

Renvoie un grand nombre de valeurs, notamment celles-ci :

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Pour obtenir des limites d’écriture, utilisez une opération d’écriture : 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Renvoie un grand nombre de valeurs, notamment celles-ci :

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Attente avant l’envoi de la requête suivante
Lorsque vous atteignez la limite de requêtes, Resource Manager renvoie le code d’état HTTP **429** et une valeur **Retry-After** dans l’en-tête. La valeur **Retry-After** spécifie le nombre de secondes pendant lesquelles votre application doit attendre (ou rester en veille) avant d’envoyer la requête suivante. Si vous envoyez une requête avant l’écoulement du temps d’attente avant une nouvelle tentative, votre requête n’est pas traitée et une autre valeur de nouvelle tentative est renvoyée.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple PowerShell complet, consultez [Vérifier les limites de Resource Manager pour un abonnement](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Pour plus d’informations sur les limites et les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
* Pour en savoir plus sur la gestion des demandes REST asynchrones, consultez [Suivi des opérations asynchrones Azure](resource-manager-async-operations.md).
