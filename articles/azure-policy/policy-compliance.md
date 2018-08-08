---
title: Obtention de données de conformité dans Azure Policy
description: Les évaluations et les effets d’Azure Policy déterminent la conformité. Découvrez comment obtenir des détails sur la conformité.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f2283125aff705aae87b6260b48deee01aa12f0d
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343550"
---
# <a name="getting-compliance-data"></a>Obtention de données de conformité

Azure Policy offre, entre autres avantages, un insight et des contrôles sur les ressources d’un abonnement ou un [groupe d’administration](../azure-resource-manager/management-groups-overview.md) d’abonnements. Ce contrôle peut être effectué de différentes façons, notamment en empêchant la création de ressources au mauvais emplacement, en appliquant une utilisation commune et cohérente des balises ou en auditant les ressources existantes pour vérifier l’adéquation des configurations et paramètres. Dans tous les cas, les données générées par Azure Policy vous permettent de comprendre l’état de conformité de votre environnement.

Il existe plusieurs façons d’accéder aux informations de conformité générées par votre stratégie et vos affectations initiatives :

- À l’aide du [portail Azure](#portal)
- À l’aide de scripts de [ligne de commande](#command_line)

Avant d’examiner les méthodes de rapport sur la conformité, voyons à quel moment les informations de conformité sont mises à jour et passons en revue la fréquence et les événements qui déclenchent un cycle d’évaluation.

## <a name="evaluation-triggers"></a>Déclencheurs d’évaluation

Les résultats d’un cycle d’évaluation terminé sont répercutés dans le fournisseur de ressources `Microsoft.PolicyInsights` via les opérations `PolicyStates` et `PolicyEvents`. Pour plus d’informations sur les options et fonctionnalités de l’API REST de stratégie Insights, consultez la page [Policy Insights](/rest/api/policy-insights/).

Différents événements permettent d’évaluer les stratégies et initiatives assignées :

- Affectation d’une nouvelle stratégie ou initiative à une étendue. Lorsque cela se produit, il faut environ 30 minutes pour que l’affectation soit appliquée à l’étendue définie. Une fois celle-ci appliquée, le cycle d’évaluation commence pour les ressources de cette étendue compte tenu de la nouvelle stratégie ou initiative ; de plus, selon les effets utilisés par la stratégie ou l’initiative, les ressources sont marquées comme conformes ou non conformes. L’évaluation d’une stratégie ou d’une initiative volumineuse sur une grande étendue de ressources peut prendre un temps, ce qui signifie qu’il est impossible de déterminer à l’avance à quel moment s’achèvera le cycle d’évaluation. Une fois le cycle terminé, les résultats de conformité à jour sont disponibles dans le portail et dans les kits de développement logiciel.
- Mise à jour d’une stratégie ou initiative déjà assignée à une étendue. Dans ce scénario, le cycle et le temps d’évaluation sont les mêmes que pour le cas d’une nouvelle affectation à une étendue.
- Déploiement d’une ressource dans une étendue avec une assignation via le Gestionnaire des ressources, REST, Azure CLI ou Azure PowerShell. Dans ce scénario, l’événement d’effet (ajout, audit, refus, déploiement) et l’état de conformité deviennent disponibles dans le portail et les Kits de développement logiciel (SDK) environ 15 minutes plus tard. Cet événement n’entraîne pas une évaluation des autres ressources.
- Cycle d’évaluation de conformité standard. Les affectations sont automatiquement réévaluées une fois par tranche de 24 heures. L’évaluation d’une stratégie ou d’une initiative volumineuse sur une grande étendue de ressources peut prendre un temps, ce qui signifie qu’il est impossible de déterminer à l’avance à quel moment s’achèvera le cycle d’évaluation. Une fois le cycle terminé, les résultats de conformité à jour sont disponibles dans le portail et dans les kits de développement logiciel.

## <a name="how-compliance-works"></a>Principe de fonctionnement de la conformité

Dans une attribution, une ressource est dite non conforme si elle ne respecte pas les règles de l’initiative ou de la stratégie. Le tableau suivant montre comment les différents effets des stratégies fonctionnent avec l’évaluation des conditions pour l’état de conformité résultant :

| État de la ressource | Résultat | Évaluation de la stratégie | État de conformité |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nouveau | Audit, AuditIfNotExist\* | True | Non conforme |
| Nouveau | Audit, AuditIfNotExist\* | False | Conforme |

\* Les effets Append, DeployIfNotExist et AuditIfNotExist nécessitent que l’instruction IF ait la valeur TRUE.
Les effets nécessitent également que la condition d’existence ait la valeur FALSE pour être non conformes. Lorsque la valeur est TRUE, la condition IF déclenche l’évaluation de la condition d’existence pour les ressources associées.

Supposons, par exemple, que vous disposiez d’un groupe de ressources (ContosoRG), comprenant des comptes de stockage (en rouge) qui sont exposés sur des réseaux publics.

![Comptes de stockage exposés sur des réseaux publics](media/policy-insights/resource-group01.png)

Dans cet exemple, vous devez faire attention aux risques de sécurité. Maintenant que vous avez créé une attribution de stratégie, elle est évaluée pour tous les comptes de stockage du groupe de ressources ContosoRG. Elle effectue l’audit des trois comptes de stockage non conformes et en modifie donc l’état pour afficher un état **non conforme.**

![Audit des comptes de stockage non conformes](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portail

Le portail Azure permet de visualiser et comprendre l’état de conformité de votre environnement selon une représentation graphique. Sur la page **Stratégie**, l’option **Vue d’ensemble** fournit des détails sur les étendues disponibles pour la conformité des stratégies et des initiatives. En complément de l’état de conformité et du nombre par affectation, elle contient un graphique retraçant la conformité au cours des sept derniers jours. La page **Conformité** regroupe essentiellement les mêmes informations (à l’exception du graphique), mais avec également des options de tri et de filtrage supplémentaires.

![Page Conformité de la stratégie](media/policy-compliance/compliance-page.png)

Une stratégie ou une initiative peut être affectée à différentes étendues. Notez dans le tableau l’étendue pour chaque affectation et le type de définition qui a été affecté à cette étendue. Le nombre de stratégies et de ressources non conformes est également indiqué pour chaque affectation. En cliquant sur une stratégie ou une initiative dans le tableau, vous obtenez davantage de détails sur la conformité de l’affectation concernée.

![Détails de conformité de la stratégie](media/policy-compliance/compliance-details.png)

Si la liste des ressources figurant sous l’onglet **Ressources non conformes** reflète l’état d’évaluation des ressources existantes pour l’affectation en cours, les événements (ajout, audit, refus, déploiement) déclenchés par la demande de création d’une ressource sont répertoriés sous l’onglet **Événements**.

![Événements de conformité de la stratégie](media/policy-compliance/compliance-events.png)

Cliquez avec le bouton droit sur la ligne de l’événement pour lequel vous souhaitez obtenir plus de détails et sélectionnez **Afficher les journaux d’activité**. La page Journal d’activité s’ouvre et les critères de recherche sont préfiltrés pour montrer les détails de l’affectation et des événements. Le journal d’activité fournit davantage de contexte ainsi que des informations supplémentaires sur ces événements.

![Journal d’activité de la stratégie de conformité](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Ligne de commande

Les informations disponibles dans le portail peuvent être récupérées directement à l’aide de l’API REST (y compris avec [ARMClient](https://github.com/projectkudu/ARMClient)) ou à l’aide d’Azure PowerShell avec l’API REST. Pour plus d’informations sur l’API REST, consultez la référence [Policy Insights](/rest/api/policy-insights/). Les pages de référence de l’API REST contiennent, pour chaque opération, un bouton vert qui vous permet de la tester directement dans votre navigateur.

Pour utiliser les exemples suivants dans Azure PowerShell, créez un jeton d’authentification avec cet exemple de code. Remplacez ensuite le $restUri par la chaîne de votre choix dans les exemples pour récupérer un objet JSON pouvant être analysé.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Synthétiser les résultats

L’API REST permet d’effectuer une synthèse selon différents critères : groupe d’administration, abonnement, groupe de ressources, ressource, initiative, stratégie, affectation au niveau de l’abonnement ou affectation au niveau du groupe de ressources. Voici un exemple de synthèse obtenu au niveau de l’abonnement à l’aide de la fonction de [résumé de l’abonnement](/rest/api/policy-insights/policystates/summarizeforsubscription) de Policy Insights :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

La sortie offre une synthèse de l’abonnement. Dans l’exemple de sortie ci-dessous, la conformité est synthétisée sous **value.results.nonCompliantResources** et **value.results.nonCompliantPolicies**. Cette requête fournit des détails supplémentaires, notamment sur chaque affectation concernée par la non-conformité, fournit des informations sur la définition de chaque affectation. Chaque objet de stratégie dans la hiérarchie fournit un **queryResultsUri** qui peut être utilisé pour obtenir des détails supplémentaires à ce niveau.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Interroger des ressources

En reprenant l’exemple ci-dessus, **value.policyAssignments.policyDefinitions.results.queryResultsUri** nous donnait un exemple d’URI pour obtenir toutes les ressources non conformes pour une définition de stratégie spécifique. Si l’on observe la valeur **$filter**on remarque que IsCompliant est défini sur false, que PolicyAssignmentId est spécifié pour la définition de stratégie, suivi de PolicyDefinitionId.
Le PolicyAssignmentId est inclus dans le filtre car le PolicyDefinitionId peut figurer dans plusieurs stratégies ou affectations initiatives avec une diversité d’étendues. En spécifiant à la fois le PolicyAssignmentId et le PolicyDefinitionId, nous pouvons être explicites dans les résultats que nous recherchons. Nous avons utilisé auparavant la valeur **latest** pour PolicyStates (la seule valeur autorisée pour **policyStatesSummaryResource** sur l’opérateur de synthèse de l’abonnement), qui définit automatiquement une fenêtre temporelle **from** et **to** pour les 24 dernières heures.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

L’exemple de réponse ci-dessous a été tronqué pour n’afficher qu’une seule ressource non conforme par souci de concision (notez que @odata.count est bien égal à 15 et correspond au nombre de ressources non conforme de l’exemple ci-dessus). La réponse détaillée fournit plusieurs éléments de données sur la ressource, la stratégie (ou initiative) et l’affectation. Notez que vous pouvez également voir quels paramètres d’affectation ont été transmis à la définition de la stratégie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visualiser les événements

Lorsqu’une ressource est créée ou mise à jour, un résultat d’évaluation de stratégie est généré. Ces résultats sont appelés _événements de stratégie_. L’URI suivant permet d’afficher les événements de stratégie récents associés à l’abonnement.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Vos résultats doivent ressembler à l’exemple suivant :

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Pour plus d’informations sur l’interrogation des événements de stratégie, consultez l’article de référence intitulé [Événements de stratégie](/rest/api/policy-insights/policyevents).

### <a name="azure-powershell"></a>Azure PowerShell

Le module Azure PowerShell de Policy est disponible dans PowerShell Gallery sous le nom [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). Vous pouvez utiliser PowerShellGet pour installer le module à l’aide de `Install-Module -Name AzureRM.PolicyInsights` (vérifiez que vous disposez de la dernière version [d’Azure PowerShell](/powershell/azure/install-azurerm-ps)) :

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Le module comporte trois applets de commande :

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Exemple : obtenir le résumé d’état de la stratégie affectée au premier plan qui comporte le plus grand nombre de ressources non conformes.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemple : obtenir l’enregistrement de l’état pour la ressource évaluée la plus récemment (par défaut, selon un horodatage dans l’ordre décroissant).

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemple : obtenir des détails pour toutes les ressources non conformes du réseau virtuel.

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemple : obtenir des événements liés aux ressources non conformes du réseau virtuel qui se sont produits après une date spécifique.

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Le champ **PrincipalOid** peut être utilisé pour obtenir un utilisateur spécifique avec l’applet de commande `Get-AzureRmADUser` d’Azure PowerShell. Remplacez **{principalOid}** par la réponse que vous obtenez à partir de l’exemple précédent.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Si vous avez un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md) dans lequel la solution `AzureActivity` est liée à votre abonnement, vous pouvez également afficher les résultats non conformes à partir du cycle d’évaluation en utilisant de simples Kusto et la table `AzureActivity`. Avec les détails de non-conformité contenus dans Log Analytics, cela signifie également que des alertes peuvent être configurées pour surveiller la non-conformité sur une ressource spécifique, un groupe de ressources ou même un seuil d’éléments non conformes (par exemple, plus de 10 éléments non conformes au cours des dernières 24 heures).

![Conformité de la stratégie à l’aide de Log Analytics](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Étapes suivantes

- Consultez la page [Structure de définition Azure Policy](policy-definition.md).
- Consultez la page [Compréhension des effets de Policy](policy-effects.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../azure-resource-manager/management-groups-overview.md).