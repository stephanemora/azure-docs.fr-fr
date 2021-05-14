---
title: Vue d'ensemble des balises de service de Pare-feu Azure
description: Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529546"
---
# <a name="azure-firewall-service-tags"></a>Balises de service de Pare-feu Azure

Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Les balises de service de Pare-feu Azure peuvent être utilisées dans le champ de destination des règles réseau. Vous pouvez les utiliser à la place d'adresses IP spécifiques.

## <a name="supported-service-tags"></a>Balises de service prises en charge

Consultez la section [Balises de service du réseau virtuel](../virtual-network/service-tags-overview.md#available-service-tags) pour obtenir la liste des balises de service disponibles que vous pouvez utiliser dans les règles de réseau de pare-feu Azure.

## <a name="configuration"></a>Configuration

Le pare-feu Azure prend en charge la configuration de balises de service via PowerShell, Azure CLI ou le Portail Azure.

### <a name="configure-via-azure-powershell"></a>Configurer via Azure PowerShell

Dans cet exemple, nous devons d’abord connaître le contexte de notre instance de pare-feu Azure précédemment créée.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Ensuite, nous devons créer une règle.  Pour la source ou la destination, vous pouvez spécifier la valeur texte de la balise de service que vous souhaitez utiliser, comme mentionné plus haut dans cet article.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Ensuite, nous devons mettre à jour la variable contenant notre définition de pare-feu Azure avec les nouvelles règles de réseau que nous avons créées.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Enfin, nous devons valider les modifications apportées aux règles de réseau sur l’instance de pare-feu Azure en cours d’exécution.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les règles du service Pare-feu Azure, consultez [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).
