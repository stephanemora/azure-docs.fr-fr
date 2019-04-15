---
title: Introduction au pare-feu d’applications web pour Azure Application Gateway
description: Cet article fournit une vue d’ensemble du pare-feu d’applications web (WAF) pour la passerelle Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518182"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Pare-feu d’applications Web pour Azure Application Gateway

Azure Application Gateway offre un pare-feu d’applications web (WAF) qui fournit une protection centralisée de vos applications web contre les vulnérabilités et exploits courants. Applications Web sont plus en plus ciblées par des attaques malveillantes qui exploitent les vulnérabilités connues. L’injection SQL et les scripts intersites font partie des attaques les plus courantes.

Il est difficile d’empêcher ces attaques dans le code d’application. Il peut nécessiter une maintenance rigoureuse, mise à jour corrective et la surveillance sur plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé permet de rendre la gestion de la sécurité beaucoup plus simple. Un pare-feu WAF offre également une meilleure garantie de protection contre les menaces et intrusions administrateurs d’application.

Une solution WAF peut réagir à une menace de sécurité plus rapide par manière centralisée mise à jour corrective une vulnérabilité connue, au lieu de la sécurisation de chaque application web individuelles. Passerelles d’application existantes peuvent facilement être convertis en passerelles d’application prenant en charge le mur incendie.

L’Application Gateway WAF est basé sur [Core règle définie (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9 à partir de l’Open Web Application Security Project (OWASP). Le pare-feu WAF met automatiquement à jour pour inclure une protection contre les nouvelles vulnérabilités, sans aucune configuration supplémentaire requise.

![Diagramme d’application Gateway WAF](./media/waf-overview/WAF1.png)

Application Gateway fonctionne comme un intègre application delivery controller (ADC). Elle offre l’arrêt de la couche SSL (Secure Sockets), l’affinité de session basée sur les cookies, distribution de charge de tourniquet (round-robin), le routage basé sur le contenu, possibilité d’héberger plusieurs sites Web et les améliorations de sécurité.

Améliorations de sécurité Application Gateway incluent la gestion de stratégie SSL et SSL de bout en bout pour prendre en charge. Sécurité des applications est renforcée par l’intégration du pare-feu d’applications Web dans Application Gateway. La combinaison protège vos applications web contre les vulnérabilités courantes. Et il fournit un emplacement central facile à configurer pour gérer.

## <a name="benefits"></a>Avantages

Cette section décrit les avantages principaux par Application Gateway et son WAF.

### <a name="protection"></a>Protection

* Protéger vos applications web contre les vulnérabilités web et des attaques sans modification au code principal.

* Protéger plusieurs applications web en même temps. Une instance de passerelle d’Application peut héberger jusqu'à 20 sites Web qui sont protégés par un pare-feu d’applications web.

### <a name="monitoring"></a>Surveillance

* Surveiller les attaques contre vos applications web à l’aide d’un journal WAF en temps réel. Le journal est intégré avec [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pour effectuer le suivi des alertes WAF et analyser facilement les tendances.

* L’Application Gateway WAF est intégré avec Azure Security Center. Security Center fournit une vue centralisée de l’état de sécurité de toutes vos ressources Azure.

### <a name="customization"></a>Personnalisation

* Vous pouvez personnaliser les règles de pare-feu d’applications Web et les groupes de règles pour les besoins de votre application et éliminer les faux positifs.

## <a name="features"></a>Caractéristiques

- Protection de l’injection de code SQL.
- Protection contre les scripts intersites.
- Protection contre les autres attaques web courantes, telles que l’injection de commande, dissimulation, fractionnement, de la réponse HTTP de la requête HTTP et inclusion d’un fichier distant.
- Protection contre les violations de protocole HTTP.
- Protection contre les anomalies de protocole HTTP, tels que l’absence agent-utilisateur hôte et en-têtes accept.
- Protection contre les robots, les robots et les scanneurs.
- Détection d’application courantes (par exemple, Apache et IIS).
- Taille de la demande configurable limite avec les limites inférieure et supérieure.
- Listes d’exclusion vous permettent d’omettre certains attributs de la demande à partir d’une version d’évaluation de WAF. Un exemple courant est inséré par Active Directory de jetons qui sont utilisés pour l’authentification ou les champs de mot de passe.

### <a name="core-rule-sets"></a>Ensembles de règles de base

Application Gateway prend en charge les deux ensembles de règles CRS 3.0 et CRS 2.2.9. Ces règles protègent vos applications web à partir d’activités malveillantes.

Le pare-feu WAF Application Gateway est préconfiguré avec CRS 3.0 par défaut. Mais vous pouvez choisir d’utiliser à la place des CRS 2.2.9. CRS 3.0 permet une réduction des faux positifs par rapport aux CRS 2.2.9. Vous pouvez également [personnaliser les règles pour l’adapter à vos besoins](application-gateway-customize-waf-rules-portal.md).

Le pare-feu WAF protège contre les vulnérabilités web suivant :

- Attaques d’injection SQL
- Attaques de script entre sites
- Autres attaques courantes, telles que l’injection de commande, HTTP demande par dissimulation, fractionnement de réponse HTTP et l’inclusion de fichier distant
- Violations de protocole HTTP
- Les anomalies de protocole HTTP, tels que l’absence agent-utilisateur hôte et les en-têtes accept
- Robots, les robots et les scanneurs
- Application courantes (par exemple, Apache et IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 inclut 13 groupes de règles, comme illustré dans le tableau suivant. Chaque groupe contient plusieurs règles, qui peuvent être désactivées.

|Groupe de règles|Description|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Méthodes de verrouillage (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Protection contre les scanneurs de port et d’environnement|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Protection contre le protocole et les problèmes de codage|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Protection contre l’injection d’en-tête, dissimulation de requête et fractionnement de réponse|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Protection contre les attaques de fichier et chemin d’accès|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Protection contre les attaques par inclusion de fichier distant|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Protéger à nouveau les attaques de l’exécution de code à distance|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Protection contre les attaques par injection de code PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Protection contre les attaques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Protection contre les attaques par injection SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Protection contre les attaques par fixation de session|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9.

CRS 2.2.9 inclut 10 groupes de règles, comme illustré dans le tableau suivant. Chaque groupe contient plusieurs règles, qui peuvent être désactivées.

|Groupe de règles|Description|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Protection contre les violations de protocole (par exemple, les caractères non valides ou une opération GET avec un corps de demande)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Protéger les informations d’en-tête incorrectes|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Protection contre les arguments ou les fichiers qui dépassent les limites|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Protection contre les méthodes restreintes, des en-têtes et des types de fichiers|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Protection contre les scanneurs et les robots|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Protection contre les attaques génériques (par exemple, par fixation de session, inclusion de fichier distant et injection de code PHP)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Protection contre les attaques par injection SQL|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Protection contre les attaques de script entre sites|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Protection contre les attaques par traversée de chemin d’accès|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Protection contre les chevaux de Troie|

### <a name="waf-modes"></a>Modes WAF

L’Application Gateway WAF peut être configuré pour s’exécuter dans les deux modes suivants :

* **Mode de détection**: Surveille et consigne toutes les alertes de menace. Vous activer la journalisation des diagnostics pour Application Gateway dans le **Diagnostics** section. Vous devez également vous assurer que le journal WAF est sélectionné et activé. Pare-feu d’applications Web ne bloque pas les demandes entrantes qu’elle s’exécute en mode de détection.
* **Mode de prévention**: Intrusions de blocs et les attaques qui détectent les règles. L’attaquant reçoit une exception « 403 tout accès non autorisé », et la connexion est interrompue. Mode de prévention enregistre ces attaques dans les journaux WAF.

### <a name="anomaly-scoring-mode"></a>Mode de score d’anomalie
 
OWASP comporte deux modes pour décider s’il faut bloquer le trafic : Mode traditionnel et le mode de calcul de score d’anomalie.

En mode traditionnel, le trafic qui correspond à n’importe quelle règle est considérée comme indépendamment de toutes les correspondances de règle. Ce mode est facile à comprendre. Mais l’absence d’informations sur le nombre de règles qui correspondent à une demande spécifique est une limitation. Par conséquent, le mode de calcul de score d’anomalie a été introduit. Il est la valeur par défaut pour les 3 OWASP. *x*.

En mode de calcul de score d’anomalie, le trafic qui correspond à aucune règle n’est pas bloqué immédiatement lorsque le pare-feu est en mode de prévention. Les règles ont un niveau de gravité spécifique : *Critique*, *erreur*, *avertissement*, ou *avis*. Ce niveau de gravité affecte une valeur numérique pour la demande, ce qui est appelée le Score d’anomalie. Par exemple, un *avertissement* règle de correspondance contribue 3 au score. Un *critique* règle de correspondance contribue à 5.

Il existe un seuil de 5 pour le Score d’anomalie à bloquer le trafic. Par conséquent, un seul *critique* correspondance de règle est suffisant pour l’Application Gateway WAF bloquer une demande, même en mode de prévention. Mais un *avertissement* règle de correspondance augmente uniquement l’anomalie Score par 3, ce qui n’est pas suffisant en lui-même pour bloquer le trafic.

> [!NOTE]
> Le message est consigné lorsqu’une règle de pare-feu d’applications Web correspond au trafic inclut la valeur de l’action « Bloqué ». Mais le trafic est en fait uniquement bloqué pour un Score d’anomalie de 5 ou version ultérieure.  

### <a name="waf-monitoring"></a>Surveillance de WAF

Il est important de surveiller l’état de votre passerelle d’application. Surveiller l’intégrité de votre pare-feu d’applications Web et les applications qu’il protège est pris en charge par l’intégration avec Azure Security Center, Azure Monitor, et les journaux d’Azure Monitor.

![Diagramme de diagnostic d’Application Gateway WAF](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Journaux de passerelle d’application sont intégrés avec [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Cela vous permet le suivi des informations de diagnostics, y compris les journaux et alertes WAF. Vous pouvez accéder à cette fonctionnalité sur le **Diagnostics** onglet dans la ressource de passerelle d’Application dans le portail ou directement par le biais d’Azure Monitor. Pour en savoir plus sur l’activation des journaux, consultez [diagnostics Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Centre de sécurité](../security-center/security-center-intro.md) vous aide à vous empêchez, détectez et répondez aux menaces. Il fournit une visibilité et contrôle de la sécurité de vos ressources Azure. Application Gateway est [intégrées à Security Center](application-gateway-integration-security-center.md). Security Center analyse votre environnement pour détecter les applications web non protégées. Il peut vous recommander d’Application Gateway WAF de protéger ces ressources vulnérables. Vous créez les pare-feux directement à partir de Security Center. Ces instances WAF sont intégrées à Security Center. Ils envoient des alertes et les informations de contrôle d’intégrité au centre de sécurité pour les rapports.

![Fenêtre de vue d’ensemble du centre de sécurité](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Journalisation

Application Gateway WAF fournit des rapports détaillés sur chaque menace qu’il détecte. La journalisation est intégrée aux journaux Azure Diagnostics. Alertes sont enregistrées au format .json. Ces journaux d’activité peuvent être intégrés aux [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Journaux de diagnostics de passerelle d’application windows](./media/waf-overview/waf2.png)

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

L’Application Gateway WAF est disponible sous un nouveau une référence (SKU). Cette référence est disponible uniquement dans le modèle d’approvisionnement Azure Resource Manager, et non dans le modèle de déploiement classique. En outre, la référence SKU WAF est fourni uniquement dans les moyennes et grandes tailles d’instance Application Gateway. Toutes les limites pour la passerelle d’Application s’appliquent également à la référence SKU WAF.

Tarification est basée sur un tarif d’instance de passerelle horaire et un coût de traitement des données. [Tarification Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) pour la référence WAF diffère des frais de référence (SKU) standard. Les frais de traitement de données sont les mêmes. Aucuns frais ne sont par règle ou groupe de règles. Vous pouvez protéger plusieurs applications web derrière le même pare-feu d’application web. Vous n’êtes pas facturé pour prendre en charge de plusieurs applications.

## <a name="next-steps"></a>Étapes suivantes

Consultez [comment configurer le pare-feu d’applications web sur Application Gateway](tutorial-restrict-web-traffic-powershell.md).
