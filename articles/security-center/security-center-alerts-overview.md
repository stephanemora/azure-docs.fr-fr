---
title: Alertes de sécurité dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes de sécurité et les différents types disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3cd3cef4d70ea3f78d7e135ad01e0e00b38c60b9
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666276"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertes de sécurité dans Azure Security Center

Dans Azure Security Center, il existe une variété d’alertes pour les nombreux types de ressources. Security Center génère des alertes pour les ressources déployées sur Azure et pour les ressources déployées sur les environnements cloud hybride et locaux.

Les détections avancées sont disponibles dans le niveau Standard d’Azure Security Center. Un essai gratuit est disponible. Vous pouvez effectuer une mise à niveau à partir de la sélection du niveau tarifaire dans la [Stratégie de sécurité](security-center-pricing.md). Pour plus d’informations sur la tarification, consultez la page [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) .

## Répondre aux menaces actuelles <a name="respond-threats"> </a>

Les menaces ont fortement évolué au cours des 20 dernières années. Dans le passé, les entreprises avaient uniquement à se soucier de la dégradation de site web par des personnes malveillantes isolées cherchant principalement à découvrir « jusqu’où elles pouvaient aller ». Les pirates d’aujourd’hui sont beaucoup plus sophistiqués et organisés. Ils ont souvent des objectifs financiers et stratégiques spécifiques. Ils ont également davantage de ressources disponibles, car ils peuvent être financés par des États-nations ou encore par le crime organisé.

Ces évolutions ont conduit à un niveau inédit de professionnalisme dans les rangs des attaquants. Ces derniers ne s’intéressent plus à la dégradation de site web, mais plutôt au vol d’informations, aux comptes financiers et aux données privées qu’ils peuvent utiliser pour générer des liquidités sur le marché ouvert ou pour exploiter une activité commerciale particulière, ou encore une position politique ou militaire. Les pirates qui violent les réseaux afin de nuire à l’infrastructure et aux personnes constituent une menace bien plus importante que ceux avec des objectifs financiers.

Face au problème, les organisations déploient souvent plusieurs solutions, axées sur la défense du périmètre de l’entreprise ou de points de terminaison, en recherchant des signatures d’attaques connues. Ces solutions ont tendance à générer un volume élevé d’alertes basse fidélité, qui nécessitent qu’un analyste les trie et les examine. La plupart des organisations ne disposent pas du temps et de l’expertise nécessaires pour répondre à ces alertes, qui restent alors bien souvent sans réponse.  

En outre, les attaquants ont fait évoluer leurs méthodes pour compromettre les nombreuses défenses basées sur la signature et pour [s’adapter aux environnements de cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). De nouvelles approches sont nécessaires pour identifier plus rapidement les nouvelles menaces, et accélérer la détection et la réaction.

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?

Les alertes sont les notifications que Security Center génère lorsqu'il détecte des menaces sur vos ressources. Security Center hiérarchise et répertorie les alertes ainsi que les informations vous permettant d’analyser rapidement le problème. Security Center fournit également des suggestions sur la manière dont vous pouvez corriger les problèmes liés à une attaque.

## Comment Security Center détecte-t-il les menaces ? <a name="detect-threats"> </a>

Les chercheurs en sécurité de Microsoft sont constamment à l’affût des nouvelles menaces. Grâce à la présence globale de Microsoft sur le cloud et localement, ils ont accès à un vaste jeu de télémétrie. La collection diverse et étendue de jeux de données permet de détecter de nouveaux modèles et de nouvelles tendances d’attaques dans ses produits locaux destinés au consommateur et aux entreprises, ainsi que dans ses services en ligne. Par conséquent, Azure Security Center peut rapidement mettre à jour ses algorithmes de détection, puisque les pirates sont à l’origine d’attaques innovantes de plus en plus sophistiquées. Cette approche permet de faire face à des menaces en pleine mutation.

Pour détecter les menaces réelles et réduire le nombre de faux positifs, Security Center collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure et du réseau. Cela fonctionne également avec les solutions de partenaires connectées, comme les solutions de pare-feu et de protection de point de terminaison. Security Center analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces.

![Collecte et présentation des données dans Azure Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Azure Security Center emploie des analyses de sécurité avancées allant bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) sont mises à profit pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques. Ces analyses de sécurité comprennent les éléments suivants :

* **Threat Intelligence intégrée** : recherche les éléments malveillants connus en exploitant les informations globales de Threat Intelligence provenant des produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes.
* **Analytique comportementale** : applique des modèles connus pour détecter les comportements malveillants.
* **Détection des anomalies** : utilise le profilage statistique pour créer une base de référence. Il avertit sur les écarts par rapport aux lignes de base établies qui se conforment à un vecteur d’attaque potentielle.

Les sections suivantes décrivent plus en détail chacune de ces données analytiques.

### <a name="integrated-threat-intelligence"></a>Threat Intelligence intégrée

Microsoft dispose d’une multitude d’informations en matière de menaces à l’échelle mondiale. La télémétrie provient de plusieurs sources, telles qu’Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) et Microsoft Security Response Center (MSRC). Les chercheurs reçoivent aussi les informations sur les menaces partagées par les principaux fournisseurs de services cloud et les flux en provenance d’autres tiers. Azure Security Center peut utiliser ces informations pour vous alerter en cas de menaces provenant d’éléments malveillants connus.

### <a name="behavioral-analytics"></a>Analyse comportementale

L’analyse comportementale est une technique qui analyse et compare les données à une collection de modèles connus. Toutefois, ces modèles ne sont pas de simples signatures. Ils sont déterminés par le biais d’algorithmes d’apprentissage automatique appliqués aux ensembles de données massifs. Ils sont également déterminés à travers une analyse minutieuse des comportements malveillants par des experts. Azure Security Center peut utiliser l’analyse comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux d’activité de la machine virtuelle, des journaux d’activité du périphérique réseau virtuel, des journaux d’activité Service Fabric, des vidages sur incident et d’autres sources.

De plus, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée. Ce rapprochement permet d’identifier les événements qui sont cohérents avec les indicateurs de compromission établis. 

### <a name="anomaly-detection"></a>Détection des anomalies

Azure Security Center utilise également la détection des anomalies pour identifier les menaces. Contrairement à l’analyse comportementale (qui dépend des modèles connus dérivés de grands jeux de données), la détection des anomalies est plus « personnalisée » et se concentre sur les lignes de base propres à vos déploiements. L’apprentissage automatique est appliqué pour déterminer l’activité normale de vos déploiements et les règles sont générées pour définir les conditions de valeurs aberrantes pouvant signaler un événement de sécurité.

## <a name="how-are-alerts-classified"></a>Comment les alertes sont-elles classifiées ?

Security Center affecte un degré de gravité aux alertes pour vous aider à hiérarchiser l’ordre dans lequel vous remédiez à chaque alerte. Ainsi, quand une ressource est compromise, vous pouvez vous en occuper immédiatement. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

> [!NOTE]
> La gravité des alertes s’affiche différemment dans le portail et dans l’API REST. Les différences sont indiquées dans la liste ci-dessous.

* **Élevée :** il est fort probable que votre ressource ait été compromise. Vous devez étudier le problème immédiatement. Security Center est très confiant quant à l’intention malveillante et aux constats à l’origine de l’alerte. Par exemple, une alerte qui détecte l’exécution d’un outil malveillant connu, tel que Mimikatz, un outil couramment utilisé pour dérober des informations d’identification.
* **Moyenne (faible dans l’API REST)** : il s’agit probablement d’une activité suspecte pouvant indiquer qu’une ressource est compromise.
La confiance de Security Center dans l’analyse et le constat est moyenne, et la confiance quant à l’intention malveillante est moyenne à élevée. Il s’agit généralement de détections basées sur des anomalies ou l’apprentissage automatique. Par exemple, une tentative de connexion depuis un emplacement anormal.
* **Faible (informations dans l’API REST)** : cela peut être un positif sans gravité ou une attaque bloquée.
   * Security Center n’est pas suffisamment confiant sur le fait que l’intention soit malveillante, et l’activité peut être innocente. Par exemple, l’effacement des journaux est une action qui peut se produire lorsqu’un pirate tente de masquer ses traces mais, dans de nombreux cas, il s’agit d’une opération de routine effectuée par les administrateurs.
   * Security Center ne vous indique généralement pas les blocages d’attaques, sauf s’il s’agit d’un cas intéressant que nous vous suggérons d’examiner. 
* **Informations (Silencieuse dans l’API REST)** : vous voyez les alertes informatives seulement lorsque vous explorez en profondeur un incident de sécurité, ou si vous utilisez l’API REST avec un ID d’alerte spécifique. Un incident est généralement constitué de plusieurs d’alertes, dont certaines peuvent apparaître séparément comme informatives uniquement mais, dans le contexte des autres alertes, mériter un examen plus approfondi. 
 

## <a name="continuous-monitoring-and-assessments"></a>Surveillance et évaluations continues

Azure Security Center s’appuie sur des équipes de recherche de sécurité et de sciences des données au sein de Microsoft qui surveillent en continu les évolutions en matière de menaces. Cela inclut les initiatives suivantes :

* **Analyse des informations sur les menaces** : les informations sur les menaces incluent des mécanismes, des indicateurs, des implications et des conseils pratiques sur les menaces, nouvelles ou existantes. Ces informations sont partagées avec la communauté dédiée à la sécurité, et Microsoft surveille en permanence le flux des informations sur les menaces provenant de sources internes et externes.
* **Partage de signal** : les informations fournies par les équipes de sécurité sur le portefeuille complet de services cloud et locaux, de serveurs, et d’appareils de point de terminaison client de Microsoft sont partagées et analysées.
* **Spécialistes de la sécurité Microsoft** : engagement continu avec les équipes Microsoft travaillant dans des domaines de la sécurité spécialisés, tels que la forensique et la détection d’attaques web.
* **Réglage de la détection** : des algorithmes sont exécutés sur les jeux de données client réels, et les chercheurs en sécurité collaborent avec les clients pour valider les résultats. Les vrais et les faux positifs sont utilisés pour affiner les algorithmes d’apprentissage automatique.

Ces efforts combinés aboutissent à des détections nouvelles et améliorées, dont vous pouvez bénéficier instantanément sans aucune action de votre part.

## Types d’alertes de sécurité <a name="security-alert-types"> </a>

Les rubriques suivantes vous guident tout au long des différentes alertes en fonction des types de ressources :

* [Alertes liées aux serveurs et machines virtuelles IaaS](security-center-alerts-iaas.md)
* [Alertes liées au calcul natif](security-center-alerts-compute.md)
* [Alertes liées aux services de données](security-center-alerts-data-services.md)

Les rubriques suivantes expliquent comment Security Center utilise les différentes données de télémétrie qu’il collecte à partir de l’intégration à l’infrastructure Azure afin d’appliquer des couches de protection supplémentaires pour les ressources déployées sur Azure :

* [Alertes liées à la couche de services](security-center-alerts-service-layer.md)
* [Détection des menaces pour Azure WAF et Azure DDoS Protection](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Que sont les incidents de sécurité ?

Un incident de sécurité collecte des alertes associées, au lieu de lister des alertes individuelles. Security Center utilise la [corrélation des alertes intelligentes cloud](security-center-alerts-cloud-smart.md) pour mettre en corrélation différentes alertes et signaux faibles en incidents de sécurité.

À l’aide d’incidents, Security Center vous offre une vue unique d’une campagne d’attaque et de toutes les alertes associées. Cette vue vous permet de comprendre rapidement quelles actions l’attaquant a effectuées et quelles ressources ont été impactées. Pour plus d’informations, consultez [Corrélation des alertes intelligentes cloud](security-center-alerts-cloud-smart.md).

## <a name="security-alerts-in-azure-activity-log"></a>Alertes de sécurité dans Journal d'activité Azure

En plus d’être disponibles dans le portail Azure ou par programmation, les alertes et les incidents de sécurité sont audités en tant qu’événements dans [Journal d'activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Pour plus d’informations sur le schéma d’événement, consultez [Alertes de sécurité dans Journal d'activité Azure](https://go.microsoft.com/fwlink/?linkid=2114113).

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a présenté les différents types d’alertes disponibles dans Azure Security Center. Pour plus d'informations, consultez les pages suivantes :

* [Guide des opérations et de planification d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [FAQ d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)

