---
title: Informations de référence sur la surveillance des données Azure Key Vault
description: Matériel de référence important requis pour superviser Key Vault
author: msmbaldwin
ms.topic: reference
ms.author: mbaldwin
ms.service: key-vault
ms.custom: subject-monitoring
ms.date: 07/07/2021
ms.openlocfilehash: 53adbd28ad0b24b224f2427de0b108ce7f123dfd
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092525"
---
# <a name="monitoring-key-vault-data-reference"></a>Informations de référence sur la supervision des données Key Vault

Pour plus d’informations sur la collecte et l’analyse des données de surveillance pour Key Vault, consultez [Surveillance de Key Vault](monitor-key-vault.md).

## <a name="metrics"></a>Mesures


Cette section répertorie toutes les métriques de plateforme collectées automatiquement pour Key Vault.  

|Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults) |
| HSM managé | [Microsoft.KeyVault/managedhsms](/azure-monitor/platform/resource-logs-categories.#microsoftkeyvaultmanagedhsms) 

### <a name="key-vault-metrics"></a>Métriques de Key Vault

Fournisseur de ressources et type : [Microsoft.keyvault/vaults](/azure-monitor/essentials/metrics-supported#microsoftkeyvaultvaults)

| Nom | Métrique | Unité | Type | Description |
|:-------|:-----|:------------|:------------------|
| Disponibilité globale du coffre | Disponibilité      | Pourcentage    | Average | Disponibilité des demandes de coffre            | 
| Saturation globale du coffre | SaturationShoebox | Pourcentage | Average| Capacité de coffre utilisée | 
| Correspondances totales de l'API de service | ServiceApiHit | Count | Count | Nombre total de correspondances de l’API de service |
| Latence globale de l'API de service | ServiceApiLatency | Millisecondes | Average | Latence globale des demandes de l’API de service |
| Résultats totaux de l'API de service | ServiceApiResult | Count | Count | Nombre total de résultats de l’API de service |

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](/azure-monitor/platform/metrics-supported).

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

Key Vault a les dimensions suivantes associées à ses métriques :

- ActivityType
- ActivityName
- TransactionType
- StatusCode
- StatusCodeClass

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour Key Vault.

Pour obtenir des informations de référence, consultez la liste [Microsoft.KeyVault/vaults](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults).  Pour plus d’informations, consultez [Journalisation d’Azure Key Vault](logging.md).

|Type de journal de ressources | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Key Vault | [Microsoft.KeyVault/vaults](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultmanagedhsms) |
| HSM managé | [Microsoft.KeyVault/managedhsms](/azure-monitor/essentials/resource-logs-categories#microsoftkeyvaultvaults) 

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Cette section fait référence à toutes les tables Kusto d’Azure Monitor Logs pertinentes pour Key Vault et disponibles pour une requête par Log Analytics. 

|Type de ressource | Notes |
|-------|-----|
| [Key Vault](/azure/azure-monitor/reference/tables/tables-resourcetype#key-vaults) | |

Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Tables de diagnostic

Key Vault utilise les [Diagnostics Azure](/azure-monitor/reference/tables/azurediagnostics), la table [Activité Azure](/azure-monitor/reference/tables/azureactivity) et les tables de [Métriques Azure](/azure-monitor/reference/tables/azuremetrics) pour stocker les informations du journal des ressources. Les colonnes suivantes sont pertinentes.

**Diagnostics Azure**

| Propriété | Description |
|:--- |:---|
| _ResourceId | Un identificateur unique de la ressource à laquelle l’enregistrement est associé |
| CallerIPAddress | Adresse IP de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| DurationMs | Durée de l’opération en millisecondes. |
| httpStatusCode_d | Code d’état HTTP retourné par la demande (par exemple, *200*) |
| Level | Niveau de l’événement. L’une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose. |
| NomOpération | Nom de l’opération, par exemple, Alert |
| properties_s |  |
| Region_s | |
| requestUri_s | URI de la requête du client. |
| Resource | |
| ResourceProvider | Fournisseur de ressources de la ressource Azure rendant compte de la métrique. |
| ResultSignature | |
| TimeGenerated | Date et heure de création de l’enregistrement. |

## <a name="see-also"></a>Voir aussi

- Pour obtenir une description de la surveillance d’Azure Key Vault, consultez [Surveillance d’Azure Key Vault](monitor-key-vault.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resources).
