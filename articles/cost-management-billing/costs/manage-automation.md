---
title: Gérer les coûts Azure avec l’automatisation
description: Cet article explique comment gérer les coûts Azure avec l’automatisation.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449829"
---
# <a name="manage-costs-with-automation"></a>Gérer les coûts avec l’automatisation

Vous pouvez utiliser l’automatisation Cost Management pour créer un ensemble personnalisé de solutions pour récupérer et gérer les données de coût. Cet article présente des scénarios courants pour l’automatisation de Cost Management et les options disponibles en fonction de votre situation. Si vous souhaitez développer à l’aide d’API, des exemples de requêtes d’API communes sont présentés pour accélérer votre processus de développement.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatiser l’extraction de données de coût pour l’analyse hors connexion

Vous pouvez avoir besoin de télécharger vos données de coût Azure pour les fusionner avec d’autres jeux de données. Vous pouvez également vouloir intégrer les données de coût à vos propres systèmes. Différentes options sont disponibles en fonction de la quantité de données impliquées. Vous devez systématiquement disposer des autorisations Cost Management au niveau de l’étendue appropriée pour utiliser les API et les outils. Pour plus d’informations, consultez [Affecter une autorisation d’accès aux données](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Suggestions pour la gestion des jeux de données volumineux

Si votre organisation dispose d’une grande présence Azure sur un grand nombre de ressources ou d’abonnements, vous disposez d’une grande quantité de données contenant des informations sur l’utilisation. Excel ne peut souvent pas charger de tels fichiers volumineux. Dans ce cas, nous vous recommandons d’utiliser les options suivantes :

**Power BI**

Power BI permet d’ingérer et de gérer de grandes quantités de données. Si vous êtes client d’un Contrat Entreprise, vous pouvez utiliser l’application de modèle Power BI pour analyser les coûts associés à votre compte de facturation. Le rapport contient les principales vues utilisées par les clients. Pour plus d’informations, consultez [Analyser les coûts Azure avec l’application de modèle Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Connecteur de données Power BI**

Si vous souhaitez analyser vos données quotidiennement, nous vous recommandons d’utiliser le [connecteur de données Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) pour obtenir des données en vue d’une analyse détaillée. Tous les rapports que vous créez sont mis à jour par le connecteur à mesure que des coûts supplémentaires s’accumulent.

**Exportations Cost Management**

Vous n’avez peut-être pas besoin d’analyser les données quotidiennement. Dans ce cas, envisagez d’utiliser la fonctionnalité [Exportations](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) de Cost Management pour planifier des exportations de données vers un compte de stockage Azure. Vous pouvez ensuite charger les données dans Power BI en fonction des besoins, ou les analyser dans Excel si le fichier est suffisamment petit. Les exportations sont disponibles dans le portail Azure ou vous pouvez configurer des exportations avec l’[API Exportations](https://docs.microsoft.com/rest/api/cost-management/exports).

**API Détails d’utilisation**

Envisagez d’utiliser l’[API Détails d’utilisation](https://docs.microsoft.com/rest/api/consumption/usageDetails) si vous avez un petit jeu de données sur les coûts. Si vous disposez d’une grande quantité de données de coût, vous devez demander la plus petite quantité de données d’utilisation possible pour une période donnée. Pour ce faire, spécifiez un intervalle de temps réduit ou utilisez un filtre dans votre demande. Par exemple, dans un scénario où vous avez besoin de trois années de données de coût, l’API est plus performante lorsque vous effectuez plusieurs appels pour différents intervalles de temps plutôt qu’un seul appel. À partir de là, vous pouvez charger les données dans Excel pour une analyse plus poussée.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatiser la récupération avec l’API Détails d’utilisation

L’[API Détails d’utilisation](https://docs.microsoft.com/rest/api/consumption/usageDetails) offre un moyen simple d’accéder à des données de coût brutes et non agrégées qui correspondent à votre facture Azure. L’API est utile lorsque votre organisation a besoin d’une solution d’extraction de données par programme. Envisagez d’utiliser l’API si vous souhaitez analyser des jeux de données de coût plus petits. Toutefois, vous devez utiliser d’autres solutions identifiées précédemment si vous avez des jeux de données plus volumineux. Les données de l’API Détails d’utilisation sont fournies par compteur et par jour. Elles sont utilisées lors du calcul de votre facture mensuelle. La version en disponibilité générale de l’API est `2019-10-01`. Utilisez `2019-04-01-preview` pour accéder à la préversion de la réservation et aux achats sur la Place de marché Azure avec les API.

### <a name="usage-details-api-suggestions"></a>Suggestions concernant l’API Détails d’utilisation

**Planification des demandes**

Nous vous recommandons d’envoyer _une seule demande par jour_ à l’API Détails d’utilisation. Pour plus d’informations sur la fréquence à laquelle les données de coût sont actualisées et sur la façon dont l’arrondi est géré, consultez [Présentation des données de gestion des coûts](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Cibler des étendues de niveau supérieur sans filtrage**

Utilisez l’API pour récupérer toutes les données dont vous avez besoin à l’étendue ayant le niveau le plus élevé disponible. Attendez que toutes les données nécessaires soient ingérées avant de procéder à un filtrage, un regroupement ou une analyse agrégée. L’API est spécifiquement optimisée pour fournir de grandes quantités de données de coût brutes et non agrégées. Pour en savoir plus sur les étendues disponibles dans Cost Management, consultez [Comprendre et utiliser des étendues](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Une fois que vous avez téléchargé les données nécessaires pour une étendue, utilisez Excel pour analyser les données plus en détail avec les filtres et les tableaux croisés dynamiques.

## <a name="example-usage-details-api-requests"></a>Exemples de demandes envoyées à l’API Détails d’utilisation

Les exemples de demande suivants sont utilisés par les clients Microsoft pour résoudre les scénarios courants que vous pouvez rencontrer.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Obtenir les détails d’utilisation d’une étendue pendant une plage de dates spécifique

Les données retournées par la requête correspondent à la date à laquelle l’utilisation a été reçue par le système de facturation. Elles peuvent inclure des coûts issus de plusieurs factures.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Recevoir des détails sur les coûts amortis

Si vous avez besoin de coûts réels pour afficher les achats au fur et à mesure qu’ils sont cumulés, remplacez la *métrique* par `ActualCost` dans la requête suivante. Pour utiliser les coûts amortis et réels, vous devez utiliser la version `2019-04-01-preview`. La version actuelle de l’API fonctionne de la même façon que la version `2019-10-01`, à l’exception du nouvel attribut type/metric et des noms de propriété modifiés. Si vous avez un Contrat client Microsoft, vos filtres sont `startDate` et `endDate` dans l’exemple suivant.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Récupérer de volumineux jeux de données de coûts de façon récurrente avec des exportations

La fonctionnalité d’exportation est une solution permettant de planifier des vidages de données de coûts régulièrement. Il s’agit de la méthode recommandée pour récupérer des données de coût sans agrégation pour les organisations dont les fichiers d’utilisation peuvent être trop volumineux pour appeler et télécharger des données de manière fiable à l’aide de l’API Détails d’utilisation. Les données sont placées dans un compte de stockage Azure de votre choix. À partir de là, vous pouvez les charger dans vos propres systèmes et les analyser en fonction des besoins de vos équipes. Pour configurer des exportations dans le portail Azure, consultez [Exporter des données](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Latence des données et limites du taux de transfert

Nous vous recommandons d’appeler les API au maximum une fois par jour. Les données Cost Management sont actualisées toutes les quatre heures au fur et à mesure que de nouvelles données d’utilisation sont reçues de la part des fournisseurs de ressources Azure. Un appel plus fréquent ne fournit pas de données supplémentaires. Au lieu de cela, cela crée une charge accrue. Pour en savoir plus sur la fréquence de modification des données et sur la façon dont la latence des données est gérée, consultez [Présentation des données de gestion des coûts](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Code d’erreur 429 - Le nombre d’appels a dépassé les limites de débit

Pour garantir une expérience cohérente pour tous les abonnés Cost Management, les API Cost Management ont un débit limité. Lorsque vous atteignez la limite, vous recevez le code d’état HTTP `429: Too many requests`. Les limites de débit actuelles pour nos API sont les suivantes :

- 30 appels par minute : cette opération est effectuée par étendue, par utilisateur ou par application.
- 200 appels par minute : cette opération est effectuée par locataire, par utilisateur ou par application.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser les coûts Azure avec l’application de modèle Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)
- [Créer et gérer des données exportées](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) avec la fonctionnalité Exportations
- En savoir plus sur l’[API Détails d’utilisation](https://docs.microsoft.com/rest/api/consumption/usageDetails)