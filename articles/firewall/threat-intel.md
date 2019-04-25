---
title: Sur les menaces pare-feu Azure en fonction de filtrage
description: En savoir plus sur le filtrage de pare-feu Azure threat intelligence
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194029"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Filtrage de menaces de pare-feu Azure basé sur l’intelligence - version préliminaire publique

Le filtrage basé sur Threat Intelligence peut être activé pour votre pare-feu pour donner l’alerte et rejeter le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) alimente les menaces de Microsoft et est utilisé par plusieurs services, dont Azure Security Center.

![Menaces de pare-feu](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence en fonction de filtrage est actuellement en version préliminaire publique et est fourni avec un contrat de niveau de service en version préliminaire. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si threat intelligence filtrage est activé, les règles associées sont traitées avant les règles NAT, règles de réseau ou règles d’application. La version préliminaire, seuls les enregistrements de confiance le plus élevés sont inclus.

Vous pouvez choisir de simplement une alerte de journal lorsqu’une règle est déclenchée, ou vous pouvez choisir d’alerte et refuser de mode.

Par défaut, la threat intelligence filtrage est activé en mode d’alerte. Impossible de désactiver cette fonctionnalité ou de modifier le mode jusqu'à ce que l’interface du portail est disponible dans votre région.

![Sur les menaces en fonction de filtrage interface du portail](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Journaux d’activité

L’extrait de journal suivant montre une règle déclenchée :

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

- **Test sortant** -alertes de trafic sortant doivent être rarement, car cela signifie que votre environnement a été compromise. Pour aider à alertes sortant de test fonctionnent, un nom de domaine complet a été créé de test qui déclenche une alerte. Utilisez **testmaliciousdomain.eastus.cloudapp.azure.com** pour vos tests sortants.

- **Trafic entrant test** -vous pouvez vous attendre afficher les alertes sur le trafic entrant si des règles DNAT sont configurées sur le pare-feu. Cela est vrai même si des sources spécifiques sont autorisés dans la règle DNAT et le trafic est refusé dans le cas contraire. Pare-feu Azure n’envoie aucune alerte sur tous les analyseurs de port connu ; uniquement sur les analyseurs qui sont connus également s’engager dans les activités malveillantes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [exemples d’Analytique de journal de pare-feu Azure](log-analytics-samples.md)
- Découvrez comment [déployer et configurer un pare-feu d’Azure](tutorial-firewall-deploy-portal.md)
- Examinez le [rapport d’analyse décisionnelle de Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)