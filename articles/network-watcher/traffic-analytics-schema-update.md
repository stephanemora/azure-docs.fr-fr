---
title: Mise à jour du schéma d’Azure Traffic Analytics – mars 2020 | Microsoft Docs
description: Exemples de requêtes avec de nouveaux champs dans le schéma Traffic Analytics.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969062"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Exemples de requêtes avec de nouveaux champs dans le schéma Traffic Analytics (mise à jour du schéma d’août 2019)

Le [schéma Traffic Analytics Log](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) a été mis à jour pour inclure les nouveaux champs suivants : **SrcPublicIPs_s**, **DestPublicIPs_s** et **NSGRule_s**. Dans les prochains mois, les anciens champs suivants seront déconseillés : **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s** et **FlowCount_d**.
Les nouveaux champs fournissent des informations sur les adresses IP source et de destination et simplifient les requêtes.

Voici trois exemples montrant comment remplacer les anciens champs par les nouveaux.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Exemple 1 : VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s et PublicIPs_s

Nous n’avons pas à déduire les cas source et de destination pour les flux publics Azure et Externe à partir du champ FlowDirection_s pour les flux AzurePublic et ExternalPublic en particulier. Dans le cas d’une appliance virtuelle réseau (NVA, Network Virtual Appliance), le champ FlowDirection_s peut également ne pas être approprié.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Exemple 2 : NSGRules_s

Le champ précédent était au format : <Index value 0)>|<NSG_RULENAME>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>.

Auparavant, nous avions l’habitude d’agréger les données NSG et NSGRules. À présent, nous n’agrégeons plus. NSGList_s contient donc un seul NSG et NSGRules_s est également utilisé pour ne contenir qu’une seule règle. Nous avons donc supprimé le formatage compliqué ici et il est possible de trouver la même chose dans d’autres champs comme mentionné ci-dessous :

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Exemple 3 : FlowCount_d

Étant donné que nous ne recherchons pas de données dans NSG, le FlowCount_d correspond simplement à AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
Seul 1 des 4 champs ci-dessus sera différent de zéro et les trois autres auront la valeur 0. Cela indiquera l’état et le nombre dans la carte réseau où le flux a été capturé.

Si le flux est autorisé, l’un des champs précédés du préfixe « Allowed » sera rempli. Sinon, un champ précédé du préfixe « Denied » sera rempli.
Si le flux est entrant, l’un des champs avec le suffixe « \_d » comme « InFlows_d » sera rempli. Sinon, c’est « OutFlows_d » qui sera rempli.

Selon les deux conditions ci-dessus, nous savons lequel des quatre champs sera rempli.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des réponses aux questions fréquentes, consultez [Questions fréquentes (FAQ) sur Traffic Analytics](traffic-analytics-faq.md). Pour plus d’informations sur des fonctionnalités, consultez la [documentation Traffic Analytics](traffic-analytics.md)
