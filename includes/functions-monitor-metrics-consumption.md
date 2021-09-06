---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567339"
---
#### <a name="portal"></a>[Portail](#tab/portal)

Utilisez [Azure Monitor Metrics Explorer](../articles/azure-monitor/essentials/metrics-getting-started.md) pour voir dans un format graphique les données relatives aux coûts de vos applications de fonction relevant du plan Consommation. 

1. Sur le [portail Azure], accédez à votre application de fonction.

1. Dans le panneau de gauche, faites défiler vers le bas jusqu’à **Supervision** et choisissez **Mesures**.  

1. À partir de **Métrique**, choisissez **Nombre d’exécutions de la fonction** et **Somme** pour **Agrégation**. Ainsi, la somme du nombre d’exécutions pendant la période choisie est ajoutée au graphique.

    ![Définir une métrique d’application de fonction à ajouter au graphique](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. Sélectionnez **Ajouter une métrique** et répétez les étapes 2 à 4 pour ajouter **Unités d’exécution de la fonction** au graphique. 

Le graphique obtenu contient les totaux des deux métriques d’exécution dans l’intervalle de temps choisi, en l’occurrence deux heures.

![Graphe du nombre d’exécutions et des unités de la fonction](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

Étant donné que le nombre d’unités d’exécution est bien supérieur au nombre d’exécutions, le graphique montre uniquement les unités d’exécution.

Ce graphique présente un total de 1,11 milliard `Function Execution Units` consommé sur une période de deux heures, exprimé en Mo-millisecondes. Pour convertir ce total en Go-secondes, divisez-le par 1 024 000. Dans cet exemple, l’application de fonction a consommé `1110000000 / 1024000 = 1083.98` Go-secondes. Vous pouvez utiliser cette valeur et la multiplier par le prix en vigueur de la durée d’exécution figurant sur la [page des tarifs Functions](https://azure.microsoft.com/pricing/details/functions/), ce qui vous permet d’obtenir le coût de ces deux heures, en supposant que vous avez déjà utilisé tous les forfaits gratuits de durée d’exécution. 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[Azure CLI](/cli/azure/) contient des commandes pour récupérer des métriques. Vous pouvez utiliser CLI à partir d’un environnement de commande local ou directement à partir du portail en utilisant [Azure Cloud Shell](../articles/cloud-shell/overview.md). Par exemple, la commande [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) suivante retourne les données horaires sur la même période que celle utilisée précédemment.

Veillez à remplacer `<AZURE_SUBSCRIPTON_ID>` par votre ID d’abonnement Azure en exécutant la commande.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Cette commande retourne une charge utile JSON semblable à l’exemple suivant :

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Cette réponse particulière indique qu’entre `2019-09-11T21:46` et `2019-09-11T23:18`, l’application a consommé 1110000000 Mo-millisecondes (1083,98 Go-secondes).

---

[Azure portal]: https://portal.azure.com