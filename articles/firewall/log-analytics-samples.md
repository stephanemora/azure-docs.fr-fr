---
title: Journaux Azure Monitor pour Pare-feu Azure
description: Vous pouvez utiliser les journaux d’activité Azure Monitor pour analyser votre Pare-feu Azure. Un exemple de fichier est généré dans le concepteur de vues d’Azure Monitor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 2d4ed76e849385c4edecb7bd97d58087c8e5b4b3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132786"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Journaux Azure Monitor pour Pare-feu Azure

Vous pouvez utiliser les exemples de journaux Azure Monitor suivants pour analyser vos journaux Pare-feu Azure. L’exemple de fichier est créé dans le Concepteur de vues dans Azure Monitor. L’article [Concepteur de vues dans Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-view-designer) contient plus d’informations sur le concept de Concepteur de vues.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Affichage des journaux Azure Monitor

Voici comment configurer un exemple de visualisation des journaux Azure Monitor. Vous pouvez télécharger l’exemple de visualisation à partir du dépôt [azure-docs-json-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview). Le moyen le plus simple consiste à cliquer avec le bouton droit sur le lien hypertexte dans cette page, à choisir *Enregistrer sous* et à fournir un nom comme **AzureFirewall.omsview** . 

Effectuez les étapes suivantes pour ajouter la vue à votre espace de travail Log Analytics :

1. Ouvrez l’espace de travail Log Analytics dans le Portail Azure.
2. Ouvrez **Concepteur de vues** sous **Général** .
3. Cliquez sur **Importer** .
4. Recherchez et sélectionnez le fichier **AzureFirewall.omsview** que vous avez téléchargé.
5. Cliquez sur **Enregistrer** .

Voici l’aspect de la vue pour les données du journal de règles d’application :

![Données du journal de règles d’application](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

Et pour les données du journal de règles de réseau :

![Données du journal de règles de réseau]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Le Pare-feu Azure journalise les données sous AzureDiagnostics avec, en guise de catégorie, **AzureFirewallApplicationRule** ou **AzureFirewallNetworkRule** . Les données contenant les détails sont stockées dans le champ msg_s. À l’aide de l’opérateur [parse](https://docs.microsoft.com/azure/kusto/query/parseoperator), nous pouvons extraire du champ msg_s les propriétés dignes d’intérêt. Les requêtes ci-dessous extraient les informations relatives aux deux catégories.

## <a name="application-rules-log-data-query"></a>Requête portant sur les données du journal de règles d’application

La requête ci-dessous analyse les données du journal de règles d’application. Les différentes lignes de commentaire comportent des indications sur la façon dont la requête a été créée :

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

La même requête dans un format plus condensé :

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Requête portant sur les données du journal de règles de réseau

La requête suivante analyse les données du journal de règles de réseau. Les différentes lignes de commentaire comportent des indications sur la façon dont la requête a été créée :

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

La même requête dans un format plus condensé :

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Requête de données du journal Threat Intelligence

La requête suivante analyse les données du journal de règles Threat Intelligence :

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Exemples de journaux d’activité

Les exemples de journaux suivants montrent les données incluses dans une entrée de journal.

:::image type="content" source="media/log-analytics-samples/log1.png" alt-text="Capture d’écran d’une entrée de journal. Plusieurs valeurs sont visibles, telles qu’un horodatage, un protocole, un numéro de port, une action, une collection de règles et une règle." border="false":::

:::image type="content" source="media/log-analytics-samples/log2.png" alt-text="Capture d’écran d’une entrée de journal. Plusieurs valeurs sont visibles, telles qu’un horodatage, un protocole, un numéro de port, une action, une collection de règles et une règle." border="false":::

:::image type="content" source="media/log-analytics-samples/log3.png" alt-text="Capture d’écran d’une entrée de journal. Plusieurs valeurs sont visibles, telles qu’un horodatage, un protocole, un numéro de port, une action, une collection de règles et une règle." border="false":::
## <a name="next-steps"></a>Étapes suivantes

Pour découvrir les diagnostics et la supervision du Pare-feu Azure, consultez le [Tutoriel : surveiller les journaux d’activité du Pare-feu Azure et les métriques](tutorial-diagnostics.md).
