---
title: Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure
description: En savoir plus sur la fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: f6a60d7c29fc7e482e32233aa86d65a801e3f55c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582251"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure

Le filtrage basé sur Threat Intelligence peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) alimente Microsoft Threat Intelligence et est utilisé par de nombreux services, dont Azure Security Center.

![Fonctionnalité de Threat Intelligence du Pare-feu](media/threat-intel/firewall-threat.png)

Si vous avez activé le filtrage basé sur la Threat Intelligence, les règles associées sont traitées avant les règles NAT, les règles de réseau ou les règles d’application.

Vous pouvez vous contenter d'enregistrer une alerte lorsqu'une règle est déclenchée, ou vous pouvez choisir le mode d'alerte et le mode de refus.

Par défaut, le filtrage basé sur la Threat Intelligence est activé en mode d'alerte. Vous ne pouvez pas désactiver cette fonctionnalité ni changer de mode tant que l'interface du portail n'est pas disponible dans votre région.

![Interface du portail de filtrage basé sur la Threat Intelligence](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Journaux d’activité

L'extrait de journal suivant montre une règle déclenchée :

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Test

- **Test sortant** : les alertes de trafic sortant doivent être exceptionnelles, car elles signifient que votre environnement a été compromis. Pour tester le fonctionnement des alertes de trafic sortant, un nom de domaine complet de test a été créé afin de déclencher une alerte. Utilisez **testmaliciousdomain.eastus.cloudapp.azure.com** pour vos tests sortants.

- **Test entrant** : vous pouvez vous attendre à recevoir des alertes de trafic entrant si des règles DNAT sont configurées sur le pare-feu, même si seules certaines sources sont autorisées sur la règle DNAT et que le trafic est autrement refusé. Le Pare-feu Azure n’alerte pas sur tous les scanneurs de ports connus ; il alerte uniquement sur les scanneurs connus pour leurs activités malveillantes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Exemples Log Analytics pour le Pare-feu Azure](log-analytics-samples.md)
- Apprenez à [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md)
- Consultez le [Rapport des renseignements de sécurité de Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)