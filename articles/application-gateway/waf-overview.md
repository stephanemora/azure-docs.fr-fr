---
title: Présentation du pare-feu d’applications web pour Azure Application Gateway
description: Cet article fournit une vue d’ensemble du pare-feu d’applications web (WAF) pour Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991397"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Pare-feu d’applications web pour Azure Application Gateway

Azure Application Gateway offre un pare-feu d’applications web (WAF) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes. Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. L’injection de code SQL et l’exécution de scripts de site à site font partie des attaques les plus courantes.

Il est difficile d’empêcher de telles attaques dans le code d’application. Cela peut nécessiter une maintenance rigoureuse, une mise à jour corrective et la surveillance au niveau de plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé permet de simplifier considérablement la gestion de la sécurité. Un pare-feu WAF offre également aux administrateurs d’application une meilleure garantie de protection contre les menaces et intrusions .

Une solution WAF peut également réagir plus rapidement à une menace de sécurité en corrigeant une vulnérabilité connue de façon centralisée plutôt que de sécuriser individuellement chacune des applications web. Il est facile de convertir une passerelle d’application en passerelle d’application avec pare-feu activé.

Le pare-feu d’applications web d’Application Gateway suit l’[ensemble de règles de base (CRS, Core Rule Set)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9 d’OWASP (Open Web Application Security Project). Le pare-feu d’applications web se met automatiquement à jour pour inclure la protection contre les nouvelles vulnérabilités, sans aucune configuration supplémentaire requise.

![Diagramme du pare-feu d’applications web d’Application Gateway](./media/waf-overview/WAF1.png)

Application Gateway fournit de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC). Elle offre une terminaison SSL, une affinité de session basée sur les cookies, une distribution de charge par tourniquet (round robin), un routage basé sur le contenu, la possibilité d’héberger plusieurs sites web et des améliorations de sécurité.

Les améliorations de sécurité de la passerelle d’application sont la gestion des stratégies SSL ainsi que la prise en charge du chiffrement SSL de bout en bout. La sécurité des applications est renforcée par l’intégration du pare-feu d’applications web dans Application Gateway. Cette combinaison protège vos applications web contre les vulnérabilités courantes. Elle fournit un emplacement central facile à configurer pour la gestion.

## <a name="benefits"></a>Avantages

Cette section décrit les principaux avantages qu’offrent Application Gateway et son pare-feu d’applications web.

### <a name="protection"></a>Protection

* Protection de vos applications web contre les vulnérabilités et les attaques web sans modification du code principal.

* Protection simultanée de plusieurs applications web. Une instance d’Application Gateway peut héberger jusqu’à 100 sites web protégés par un pare-feu d’application web.

### <a name="monitoring"></a>Surveillance

* Surveillez les attaques dirigées contre vos applications web à l’aide d’un journal WAF en temps réel. Ce journal est intégré avec [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pour effectuer le suivi des alertes WAF et analyser facilement les tendances.

* Le pare-feu d’applications web est intégré avec Azure Security Center. Azure Security Center offre une vue centrale de l’état de sécurité de toutes vos ressources Azure.

### <a name="customization"></a>Personnalisation

* Vous pouvez personnaliser des règles et groupes de règles WAF pour les besoins de votre application et éliminer les faux positifs.

## <a name="features"></a>Caractéristiques

- Protection contre l’injection de code SQL.
- Protection contre l’exécution de scripts de site à site.
- Protection contre d’autres attaques web courantes comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et l’inclusion de fichier distant.
- Protection contre les violations de protocole HTTP.
- Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept.
- Protection contre les bots, robots d’indexation et scanneurs.
- Détection des erreurs de configuration d’application courantes (par exemple, Apache et IIS).
- Limites de taille de demande configurables avec seuils inférieur et supérieur.
- Des listes d’exclusion vous permettent d’omettre certains attributs de demande d’une évaluation de pare-feu d’applications web. À titre d’exemple courant, citons les jetons Active Directory insérés qui sont utilisés pour les champs d’authentification ou de mot de passe.

### <a name="core-rule-sets"></a>Ensembles de règles de base

Application Gateway prend en charge les ensembles de règles CRS 3.0 et CRS 2.2.9. Ces règles protègent vos applications web contre les activités malveillantes.

Le pare-feu d’applications web d’Application Gateway est préconfiguré avec CRS 3.0 par défaut. Mais vous pouvez choisir d’utiliser CRS 2.2.9 à la place. CRS 3.0 permet une réduction des faux positifs par rapport à la version 2.2.9. Vous pouvez également [personnaliser les règles en fonction de vos besoins](application-gateway-customize-waf-rules-portal.md).

Le WAF protège contre les vulnérabilités web suivantes :

- Attaques par injection de code SQL
- Attaques par exécution de scripts de site à site
- Autres attaques courantes comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)
- Violations de protocole HTTP
- Anomalies de protocole HTTP comme un agent utilisateur hôte manquant et les en-têtes Accept
- Bots, robots d’indexation et scanneurs
- Erreurs de configuration d’application courantes (par exemple, Apache et IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 inclut 13 groupes de règles, comme illustré dans le tableau suivant. Chaque groupe contient plusieurs règles qui peuvent être désactivées.

|Groupe de règles|Description|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Verrouiller les méthodes (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Protéger contre les scanneurs de port et d’environnement.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Protéger contre les problèmes de protocole et d’encodage.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Protéger contre les attaques par injection d’en-tête, dissimulation de requête et fractionnement de réponse.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Protéger contre les attaques par fichier et chemin d’accès.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Protéger contre les attaques par inclusion de fichier distant (RFI).|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Protéger contre les attaques par exécution de code à distance.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Protéger contre les attaques par injection de code PHP.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Protéger contre les attaques par exécution de scripts de site à site.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Protéger contre les attaques par injection de code SQL.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Protéger contre les attaques par fixation de session.|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 inclut 10 groupes de règles, comme illustré dans le tableau suivant. Chaque groupe contient plusieurs règles qui peuvent être désactivées.

|Groupe de règles|Description|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Protéger contre les violations de protocole (par exemple, caractères non valides, GET avec un corps de demande).|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Protéger contre des informations d’en-tête incorrectes.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Protéger contre les arguments ou fichiers qui dépassent les limites.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Protéger contre les types de fichiers, les en-têtes et les méthodes restreints.|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Protéger contre les analyseurs et les scanneurs de sites web.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Protéger contre les attaques génériques (par fixation de session, inclusion de fichier distant et injection de code PHP).|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Protéger contre les attaques par injection de code SQL.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Protéger contre les attaques par exécution de scripts de site à site.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Protéger contre les attaques par traversée de chemin.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Protéger contre les chevaux de Troie.|

### <a name="waf-modes"></a>Modes WAF

Le pare-feu d’applications web d’Application Gateway peut être configuré pour s’exécuter dans les deux modes suivants :

* **Mode de détection** : Surveille et journalise toutes les alertes de menace. Vous activez l’enregistrement des diagnostics pour Application Gateway dans la section **Diagnostics**. Vous devez également vérifier que le journal WAF est sélectionné et activé. Le pare-feu d’applications web ne bloque pas les demandes entrantes quand il opère en mode de détection.
* **Mode de prévention** : Bloque les intrusions et les attaques détectées par les règles. L’attaquant reçoit une exception « 403 Accès non autorisé » et la connexion prend fin. Le mode de prévention enregistre de telles attaques dans les journaux WAF.

### <a name="anomaly-scoring-mode"></a>Mode de scoring d’anomalie

OWASP dispose de deux modes pour décider de bloquer ou non le trafic : le mode traditionnel et le mode de calcul de scoring d’anomalie.

En mode traditionnel, le trafic correspondant à une règle est considéré indépendamment de toute autre correspondance. Ce mode est facile à comprendre. Mais le manque d’informations sur le nombre de règles correspondant à une requête spécifique est une limitation. C’est ainsi que le mode de scoring d’anomalie a été introduit. C’est le mode par défaut pour 3 OWASP.*x*.

En mode de scoring d’anomalie, le trafic correspondant à une règle n’est pas immédiatement bloqué lorsque le pare-feu est en mode de prévention. Les règles ont un niveau de gravité spécifique : *Critique*, *Erreur*, *Avertissement* ou *Avis*. Ce niveau de gravité affecte à la demande une valeur numérique appelée le score d’anomalie. Par exemple, une correspondance de règle *Avertissement* ajoute 3 au score. Une correspondance de règle *Avertissement* ajoute 5.

|severity  |Valeur  |
|---------|---------|
|Critique     |5\.|
|Error        |4|
|Avertissement      |3|
|Avis       |2|

Le score d’anomalie doit atteindre le seuil de 5 pour que le trafic soit bloqué. Ainsi, une seule correspondance de règle *Critique* suffit pour que le pare-feu d’applications web d’Application Gateway bloque une demande, même en mode de prévention. Mais une correspondance de règle *Avertissement* augmente considérablement selon le score d’anomalie de 3, ce qui est insuffisant en soi pour bloquer le trafic.

> [!NOTE]
> Le message journalisé quand une règle de pare-feu d’applications web correspond au trafic inclut la valeur d’action « Bloqué ». Mais le trafic n’est en fait bloqué que pour un score d’anomalie de 5 ou supérieur.  

### <a name="waf-monitoring"></a>Surveillance du pare-feu d’applications web

Il est important de surveiller l’état de votre passerelle d’application. La surveillance de l’intégrité de votre pare-feu d’applications web et des applications qu’il protège est prise en charge par l’intégration avec Azure Security Center, Azure Monitor et les journaux Azure Monitor.

![Diagramme des diagnostics du pare-feu d’applications web d’Application Gateway](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Le journaux d’Application Gateway sont intégrés avec [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Cela vous permet d’effectuer le suivi des informations de diagnostic, y compris des alertes et des journaux WAF. Vous pouvez accéder à cette fonctionnalité sous l’onglet **Diagnostics** dans la ressource Application Gateway du portail ou directement via Azure Monitor. Pour en savoir plus sur l’activation des journaux, voir [Diagnostics d’Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md) vous aide à vous empêcher, détecter et traiter les menaces. Il offre une visibilité et un contrôle accrus sur la sécurité de vos ressources Azure. Application Gateway est [intégré avec Azure Security Center](application-gateway-integration-security-center.md). Azure Security Center analyse votre environnement pour détecter les applications web non protégées. Il peut recommander au pare-feu d’applications web d’Application Gateway de protéger ces ressources vulnérables. Vous créez les pare-feux directement à partir de Security Center. Ces instances de pare-feu d’applications web sont intégrées avec Security Center. Elles envoient des alertes et des informations de contrôle d’intégrité au Security Center pour la génération de rapports.

![Fenêtre Vue d’ensemble de Security Center](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Journalisation

Le pare-feu d’applications web d’Application Gateway fournit des rapports détaillés sur chaque menace détectée. La journalisation est intégrée avec les journaux Azure Diagnostics. Les alertes sont enregistrées au format .json. Ces journaux d’activité peuvent être intégrés aux [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Fenêtres des journaux de diagnostics d’Application Gateway](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Tarification de la référence SKU Application Gateway WAF

Le pare-feu d’applications web d’Application Gateway est disponible sous une nouvelle référence SKU. Cette référence SKU est disponible uniquement dans le modèle d’approvisionnement Azure Resource Manager, pas dans le modèle de déploiement classique. Par ailleurs, la référence SKU du pare-feu d’applications web est proposée uniquement dans les tailles d’instances d’Application Gateway moyenne et grande. Toutes les limites applicables à Application Gateway s’appliquent également à la référence SKU du pare-feu d’applications web.

La tarification est basée sur le coût horaire d’instance de passerelle et des frais de traitement des données. La [tarification d’Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) pour la référence SKU de pare-feu d’applications web diffère de celle de la référence SKU standard. Les frais de traitement des données restent inchangés. Il n’y a pas de frais par règle ou groupe de règles. Vous pouvez protéger plusieurs applications web derrière le même pare-feu d’application web. Vous n’êtes pas facturé pour la prise en charge de plusieurs applications.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Guide pratique pour configurer le pare-feu d’applications web dans Application Gateway](tutorial-restrict-web-traffic-powershell.md).
