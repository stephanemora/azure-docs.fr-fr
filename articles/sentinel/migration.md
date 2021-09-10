---
title: Migrer vers Azure Sentinel à partir d’une solution SIEM existante
description: Découvrez comment optimiser la migration d’une solution SIEM existante vers Azure Sentinel, pour une analytique de sécurité intelligente et évolutive dans toute votre organisation.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 07/04/2021
ms.author: bagol
ms.openlocfilehash: 525def5e69317597664b54bc68505ae3f19d228e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533171"
---
# <a name="migrate-to-azure-sentinel-from-an-existing-siem"></a>Migrer vers Azure Sentinel à partir d’une solution SIEM existante

L’équipe de votre centre des opérations de sécurité (SOC) utilisera des solutions centralisées d’Informations de sécurité et gestion d’événements (SIEM) et d’orchestration, d’automatisation et de réponse en matière de sécurité (SOAR) pour protéger votre patrimoine numérique de plus en plus décentralisé.

Les SIEM héritées sont souvent locales et peuvent assurer une bonne couverture de vos ressources locales. Toutefois, les architectures locales peuvent proposer une couverture insuffisante pour vos ressources cloud, par exemple dans Azure, Microsoft 365, AWS ou Google Cloud Platform (GCP). En revanche, Azure Sentinel peut ingérer des données provenant à la fois de ressources locales et de ressources cloud, ce qui garantit une couverture de l’ensemble de votre patrimoine.

Cet article décrit comment migrer une SIEM existante héritée vers Azure Sentinel, soit dans une configuration côte à côte, soit en passant au déploiement complet d’Azure Sentinel.

## <a name="plan-your-migration"></a>Planifier votre migration

Vous avez peut-être décidé d’entamer une transition directe ou progressive vers Azure Sentinel, en fonction des besoins de votre entreprise et des ressources disponibles.

Vous voudrez planifier correctement votre migration pour vous assurer que la transition n’introduit pas de lacunes dans la couverture, ce qui pourrait mettre en péril la sécurité de votre organisation.

Pour commencer, identifiez vos capacités essentielles et vos besoins prioritaires. Évaluez les principaux cas d’usage couverts par votre SIEM actuelle et déterminez les détections et les capacités dont Azure Sentinel a besoin pour continuer à assurer la couverture.

Vous ajouterez un complément de planification en cours de processus à chaque étape de votre processus de migration, à mesure que vous examinerez les sources de données et les règles de détection exactes que vous souhaitez migrer. Pour plus d’informations, consultez [Migrer vos données](#migrate-your-data) et [Migrer des règles d’analyse](#migrate-analytics-rules).

> [!TIP]
> Votre SIEM actuelle peut comporter un nombre excessif de détections et de cas d’usage. Décidez lesquels sont les plus utiles à votre entreprise et déterminez ceux qui n’ont pas besoin d’être migrés. Par exemple, vérifiez quelles détections ont produit des résultats au cours de l’année écoulée.
>

### <a name="compare-your-legacy-siem-to-azure-sentinel"></a>Comparer votre SIEM héritée à Azure Sentinel

Comparez votre SIEM héritée à Azure Sentinel pour affiner vos critères d’achèvement de la migration et comprendre où vous pouvez tirer plus de valeur d’Azure Sentinel.

Par exemple, évaluez les domaines clés suivants :

|Zone d’évaluation |Description  |
|---------|---------|
|**Couverture de détection des attaques.**     | Comparez la capacité de chaque SIEM à détecter toute la gamme des attaques, en utilisant [MITRE ATT&CK](https://attack.mitre.org/) ou une infrastructure similaire.        |
|**Réactivité.**     |   Mesurez le délai moyen d’accusé de réception (MTTA), c’est-à-dire le temps qui s’écoule entre l’apparition d’une alerte dans la SIEM et le moment où un analyste commence à travailler dessus. Ce temps sera probablement similaire entre les SIEM.      |
|**Temps moyen de correction (MTTR).**     |    Comparez le MTTR pour les incidents examinés par chaque SIEM, en supposant que les analystes ont des niveaux de compétences équivalents.     |
|**Rapidité et souplesse de la chasse.**     | Mesurez la rapidité avec laquelle les équipes peuvent chasser, en partant d’une hypothèse entièrement formée, en interrogeant les données et en obtenant les résultats sur chaque plateforme SIEM.        |
|**Friction de la croissance de la capacité.**     |  Comparez le niveau de difficulté à ajouter de la capacité à mesure que l’utilisation augmente. Gardez à l’esprit que les applications et les services cloud ont tendance à générer plus de données de journal que les charges de travail locales traditionnelles.       |
|     |         |

Si vous avez un investissement limité ou nul dans une SIEM locale existante, la migration vers Azure Sentinel peut être un déploiement direct et simple. Toutefois, les entreprises qui ont fortement investi dans une SIEM héritée ont généralement besoin d’un processus en plusieurs étapes pour réaliser les tâches de transition.

Bien qu’Azure Sentinel fournisse des données et des réponses étendues à la fois localement et dans le cloud, vous pouvez commencer votre migration lentement, en exécutant Azure Sentinel et votre SIEM héritée [côte à côte](#select-a-side-by-side-approach-and-method). Dans une architecture côte à côte, les ressources locales peuvent utiliser la SIEM locale et les ressources cloud, et les nouvelles charges de travail utilisent les analyses informatiques.

À moins que vous ne choisissiez une configuration côte à côte à long terme, terminez votre migration vers un déploiement complet d’Azure Sentinel pour bénéficier de coûts d’infrastructure réduits, d’une analyse des menaces en temps réel et de la scalabilité cloud.

## <a name="select-a-side-by-side-approach-and-method"></a>Sélectionner une approche et une méthode côte à côte

Utilisez une architecture côte à côte soit comme une phase de transition à court terme qui mène à une SIEM entièrement hébergée dans le cloud, soit comme un modèle opérationnel à moyen et long terme, en fonction des besoins de votre organisation en matière de SIEM.

Par exemple, bien que l’architecture recommandée consiste à utiliser une architecture côte à côte juste le temps d’effectuer la migration, votre organisation peut vouloir conserver sa configuration côte à côte plus longtemps, par exemple si vous n’êtes pas prêt à abandonner votre SIEM héritée. En règle générale, les organisations qui utilisent une configuration côte à côte à long terme utilisent Azure Sentinel pour analyser uniquement leurs données cloud.

Prenez en compte les avantages et les inconvénients de chaque approche lorsque vous décidez laquelle utiliser pour votre migration.

> [!NOTE]
> De nombreuses organisations évitent d’exécuter plusieurs solutions d’analyse locales en raison de leur coût et de leur complexité.
>
> Azure Sentinel propose une [tarification de paiement à l’utilisation](azure-sentinel-billing.md) et une infrastructure flexible, ce qui donne aux équipes SOC le temps de s’adapter au changement. Migrez et testez votre contenu au rythme qui convient le mieux à votre organisation.
>
### <a name="short-term-approach"></a>Approche à court terme

:::row:::
   :::column span="":::
      **Avantages**

        - Donne au personnel SOC le temps de s’adapter aux nouveaux processus à mesure que les charges de travail et les analyses migrent.

        - Obtient une corrélation profonde sur toutes les sources de données pour les scénarios de chasse.

        - Élimine la nécessité d’effectuer des analyses entre les SIEM, de créer des règles de transfert et de fermer des investigations à deux endroits différents.

        - Permet à votre équipe SOC de déclasser rapidement les solutions SIEM héritées, en éliminant les coûts liés à l’infrastructure et aux licences.
   :::column-end:::
   :::column span="":::
      **Inconvénients**

        - Peut nécessiter une courbe d’apprentissage abrupte pour le personnel SOC.
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>Approche à moyen et long terme

:::row:::
   :::column span="":::
      **Avantages**

        - Vous permet d’utiliser les avantages clés d’Azure Sentinel, comme l’intelligence artificielle, le ML et les capacités d’investigation, sans vous éloigner complètement de votre SIEM héritée.

        - Économise de l’argent par rapport à votre SIEM héritée, en analysant les données cloud ou Microsoft dans Azure Sentinel.
   :::column-end:::
   :::column span="":::
      **Inconvénients**

        - Augmente la complexité en séparant les analyses sur différentes bases de données.

        - Fractionne la gestion des cas et les investigations pour les incidents multienvironnements.

        - Engendre des coûts de personnel et d’infrastructure plus élevés.

        - Exige que le personnel SOC connaisse deux solutions SIEM différentes.
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-azure-sentinel-recommended"></a>Envoyer des alertes d’une solution SIEM héritée à Azure Sentinel (recommandé)

Envoyez des alertes ou des indicateurs d’activité anormale de votre SIEM héritée à Azure Sentinel.

- Ingérez et analysez les données cloud dans Azure Sentinel.
- Utilisez votre SIEM héritée pour analyser les données locales et générer des alertes.
- Transférez les alertes de votre SIEM locale vers Azure Sentinel pour établir une interface unique.

Par exemple, transférez les alertes à l’aide de [Logstash](connect-logstash.md), d’[API](/rest/api/securityinsights/) ou de [Syslog](connect-syslog.md) et stockez-les au format [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) dans votre [espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) pour Azure Sentinel.

En envoyant des alertes de votre SIEM héritée à Azure Sentinel, votre équipe peut croiser les alertes et les examiner dans Azure Sentinel. L’équipe peut toujours accéder à la SIEM héritée pour une investigation plus approfondie, le cas échéant. Pendant ce temps, vous pouvez continuer à migrer des sources de données sur une période de transition prolongée.

Cette méthode de migration recommandée, côte à côte, vous permet de profiter pleinement d’Azure Sentinel et de migrer les sources de données au rythme qui convient à votre organisation. Cette approche évite de dupliquer les coûts de stockage et d’ingestion des données pendant que vous transférez vos sources de données.

Pour plus d'informations, consultez les pages suivantes :

- [Migrer les infractions de QRadar vers Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043).
- [Exporter des données de Splunk vers Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237).


### <a name="send-alerts-and-enriched-incidents-from-azure-sentinel-to-a-legacy-siem"></a>Envoyer des alertes et des incidents enrichis d’Azure Sentinel à une SIEM héritée

Analysez certaines données dans Azure Sentinel, comme les données cloud, puis envoyez les alertes générées à une SIEM héritée. Utilisez la SIEM *héritée* comme interface unique pour croiser les alertes générées par Azure Sentinel. Vous pouvez toujours utiliser Azure Sentinel pour une investigation plus approfondie des alertes générées par Azure Sentinel.

Cette configuration est économique, car vous pouvez transférer votre analyse des données cloud vers Azure Sentinel sans dupliquer les coûts ou payer deux fois pour les données. Vous avez toujours la liberté de migrer à votre propre rythme. À mesure que vous continuez à déplacer des sources de données et des détections vers Azure Sentinel, il devient plus facile de migrer vers Azure Sentinel comme interface principale. Cependant, le simple fait de transférer les incidents enrichis vers une SIEM héritée limite la valeur que vous obtenez des capacités d’investigation, de chasse et d’automatisation d’Azure Sentinel.

Pour plus d'informations, consultez les pages suivantes :

- [Envoyer des alertes Azure Sentinel enrichies à votre SIEM héritée](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [Envoyer des alertes Azure Sentinel enrichies à IBM QRadar](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Ingérer des alertes Azure Sentinel dans Splunk](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>Autres méthodes

Le tableau suivant décrit les configurations côte à côte qui ne sont *pas* recommandées, en expliquant pourquoi :

|Méthode  |Description  |
|---------|---------|
|**Envoyer des journaux Azure Sentinel à votre SIEM héritée**     |  Avec cette méthode, vous continuerez à subir les problèmes de coût et d’échelle de votre SIEM locale. <br><br>Vous devrez payer pour l’ingestion des données dans Azure Sentinel, ainsi que pour les coûts de stockage dans votre SIEM héritée, et vous ne pourrez pas tirer parti des détections de SIEM et de SOAR, des analyses, de l’analyse du comportement des entités utilisateur (UEBA), de l’intelligence artificielle ou des outils d’investigation et d’automatisation d’Azure Sentinel.       |
|**Envoyer les journaux d’une SIEM héritée vers Azure Sentinel**     |   Bien que cette méthode vous offre toutes les fonctionnalités d’Azure Sentinel, votre entreprise paie toujours pour deux sources d’ingestion des données différentes. Outre l’ajout de complexité architecturale, ce modèle peut entraîner des coûts plus élevés.     |
|**Utiliser Azure Sentinel et votre SIEM héritée comme deux solutions entièrement distinctes**     |  Vous pouvez utiliser Azure Sentinel pour analyser certaines sources de données, comme vos données cloud, et continuer d’utiliser votre SIEM locale pour les autres sources. Cette configuration permet d’établir des limites claires quant à l’utilisation de chaque solution et d’éviter la duplication des coûts. <br><br>Toutefois, la corrélation croisée devient difficile, et vous ne pouvez pas diagnostiquer entièrement les attaques qui traversent les deux ensembles de sources de données. Dans le paysage actuel, où les menaces se déplacent souvent latéralement dans une organisation, de telles lacunes de visibilité peuvent poser des risques de sécurité importants.       |
|     |         |



## <a name="migrate-your-data"></a>Migration de vos données

Veillez à migrer uniquement les données qui représentent vos principaux cas d’usage actuels.

1. Déterminez les données nécessaires à la prise en charge de chacun de vos cas d’usage.

1. Déterminez si vos sources de données actuelles fournissent des données utiles.

1. Identifiez les éventuelles lacunes de visibilité de votre SIEM actuelle et la manière dont vous pouvez les combler.

1. Pour chaque source de données, demandez-vous si vous devez ingérer des journaux bruts, ce qui peut être coûteux, ou si des alertes enrichies fournissent suffisamment de contexte pour vos principaux cas d’usage.

      Par exemple, vous pouvez ingérer des données enrichies à partir des produits de sécurité de l’entreprise et utiliser Azure Sentinel pour les corréler, sans avoir à ingérer les journaux bruts des sources de données elles-mêmes.

1. Utilisez l’une des ressources suivantes pour ingérer des données :

    - Utilisez les **[connecteurs de données intégrés](connect-data-sources.md) d’Azure Sentinel** pour commencer à ingérer des données. Par exemple, vous pouvez commencer un [essai gratuit](azure-sentinel-billing.md#free-trial) avec vos données cloud ou utiliser des [connecteurs de données gratuits](azure-sentinel-billing.md#free-data-sources) pour ingérer des données provenant d’autres produits Microsoft.

    - Utilisez **[Syslog](connect-data-sources.md#syslog), [Common Event Format (CEF)](connect-data-sources.md#common-event-format-cef) ou les [API REST](connect-data-sources.md#rest-api-integration)** pour connecter d’autres sources de données.

        Pour plus d’informations, consultez [Connecteurs de données de partenaires Azure Sentinel](partner-data-connectors.md), et le [catalogue de solutions Azure Sentinel](sentinel-solutions-catalog.md).

> [!TIP]
> - Si vous vous limitez aux sources de données gratuites, vous risquez de limiter votre capacité à tester les données qui sont importantes pour vous. Lorsque vous effectuez des tests, envisagez de limiter l’ingestion des données à partir de connecteurs de données gratuits et payants afin de tirer le meilleur parti des résultats de vos tests.
>
> - À mesure que vous migrez les détections et les cas d’usage dans Azure Sentinel, restez attentif aux données que vous ingérez et vérifiez leur valeur par rapport à vos principales priorités. Revenez sur les conversations de collecte de données pour vous assurer de la profondeur et de l’étendue des données dans vos cas d’usage.
>

## <a name="migrate-analytics-rules"></a>Migrer des règles d’analyse

Azure Sentinel utilise des analyses d’apprentissage automatique pour créer des incidents de haute fidélité et exploitables, et certaines de vos détections existantes peuvent être redondantes dans Azure Sentinel. Par conséquent, ne migrez pas aveuglément toutes vos règles de détection et d’analyse :

- Veillez à sélectionner les cas d’usage qui justifient la migration des règles, en tenant compte des priorités et de l’efficacité de l’entreprise.

- Passez en revue les [règles d’analyse intégrées](detect-threats-built-in.md) qui peuvent déjà répondre à vos cas d’usage. Dans Azure Sentinel, allez dans l’onglet **Configuration > Analyse > Modèles de règle** pour créer des règles basées sur des modèles intégrés.

- Examinez toutes les règles qui n’ont pas déclenché d’alertes au cours des 6 à 12 derniers mois et déterminez si elles sont toujours pertinentes.

- Éliminez les menaces ou les alertes de bas niveau que vous ignorez régulièrement.

**Pour migrer vos règles d’analyse vers Azure Sentinel** :

1. Vérifiez que vous avez mis en place un système de test pour chaque règle que vous souhaitez migrer.

    1. **Préparez un processus de validation** pour vos règles migrées, y compris des scénarios et des scripts de test complets.

    1. **Assurez-vous que votre équipe dispose des ressources utiles** pour tester vos règles migrées.

    1. **Confirmez que vous avez connecté toutes les sources de données requises**, et révisez vos méthodes de connexion de données.

1. Vérifiez si vos détections sont disponibles en tant que modèles intégrés dans Azure Sentinel :

    - **Si les règles intégrées sont suffisantes**, utilisez les modèles de règles intégrées pour créer des règles pour votre propre espace de travail.

        Dans Azure Sentinel, accédez à l’onglet **Configuration > Analyse > Modèles de règle**, puis créez et mettez à jour chaque règle d’analyse pertinente.

        Pour plus d’informations, consultez [Détection des menaces prête à l’emploi](detect-threats-built-in.md).

    - **Si vous avez des détections qui ne sont pas couvertes par les règles intégrées d’Azure Sentinel**, essayez un convertisseur de requêtes en ligne, comme [Uncoder.io](https://uncoder.io/) pour convertir vos requêtes en KQL.

        Identifiez la condition du déclencheur et l’action de la règle, puis créez et passez en revue votre requête KQL.

    - **Si ni les règles intégrées ni un convertisseur de règles en ligne ne suffisent**, vous devrez créer la règle manuellement. Dans ce cas, procédez comme suit pour commencer à créer votre règle :

        1. **Identifiez les sources de données que vous souhaitez utiliser dans votre règle**. Vous devrez créer une table de correspondance entre les sources de données et les tables de données dans Azure Sentinel pour identifier les tables que vous souhaitez interroger.

        1. **Identifiez tous les attributs, champs ou entités** de vos données que vous souhaitez utiliser dans vos règles.

        1. **Identifiez les critères et la logique de vos règles**. À ce stade, vous pouvez utiliser des modèles de règle comme exemples pour construire vos requêtes KQL.

            Pensez aux filtres, aux règles de corrélation, aux listes actives, aux ensembles de référence, aux listes de surveillance, aux anomalies de détection, aux agrégations, etc. Vous pouvez utiliser les références fournies par votre SIEM héritée pour comprendre comment mapper au mieux la syntaxe de vos requêtes.

            par exemple :

            - [Exemple de mappage de règles entre ArcSight/QRadar et Azure Sentinel](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [Exemples de mappage SPL vers KQL](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **Identifiez la condition du déclencheur et l’action de la règle, puis créez et passez en revue votre requête KQL**. Lorsque vous examinez votre requête, tenez compte des aides relatives à l’optimisation de KQL.

1. Testez la règle avec chacun de vos cas d’usage pertinents. Si elle ne fournit pas les résultats attendus, vous pouvez revoir la requête KQL et la tester à nouveau.

1. Lorsque vous êtes satisfait, vous pouvez considérer que la règle a été migrée. Créez un playbook pour votre action de règle le cas échéant. Pour plus d’informations, consultez [Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel](automate-responses-with-playbooks.md).

**Pour plus d’informations, consultez** :

- [**Créez des règles d’analyse personnalisées pour détecter des menaces**](detect-threats-custom.md). Utilisez le [regroupement d’alertes](detect-threats-custom.md#alert-grouping) pour réduire la fatigue des alertes en regroupant les alertes qui se produisent dans un laps de temps donné.
- [**Mettez en correspondance les champs de données avec les entités dans Azure Sentinel**](map-data-fields-to-entities.md) pour permettre aux ingénieurs SOC de définir les entités comme faisant partie des preuves à suivre pendant une enquête. Le mappage des entités permet également aux analystes SOC de tirer parti d’un [graphique d’investigation (investigate-cases.md#use-the-investigation-graph-to-deep-dive) intuitif qui peut contribuer à réduire le temps et les efforts.
- [**Examinez les incidents avec les données UEBA**](investigate-with-ueba.md), en guise d’exemple d’utilisation de preuves pour faire apparaître les événements, les alertes et les signets associés à un incident particulier dans le volet d’aperçu de l’incident.
- [**Kusto Query Language (KQL)** ](/azure/data-explorer/kusto/query/), que vous pouvez utiliser pour envoyer des requêtes en lecture seule à votre base de données [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) pour traiter les données et renvoyer les résultats. KQL est également utilisé dans d’autres services Microsoft, tels que [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender) et [Application Insights](../azure-monitor/app/app-insights-overview.md).

## <a name="use-automation-to-streamline-processes"></a>Utiliser l’automatisation pour simplifier les processus

Utilisez des flux de travail automatisés pour regrouper et classer par ordre de priorité les alertes en un incident commun et modifier sa priorité.

Pour plus d'informations, consultez les pages suivantes :

- [Orchestration, automatisation et réponse aux incidents de sécurité (SOAR) dans Azure Sentinel](automation-in-azure-sentinel.md)
- [Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel](automate-responses-with-playbooks.md)
- [Automatiser la gestion des incidents dans Azure Sentinel à l’aide de règles d’automatisation](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>Mettre hors service votre SIEM héritée

Utilisez la liste de vérification suivante pour vous assurer que vous avez effectué une migration complète vers Azure Sentinel et que vous êtes prêt à mettre hors service votre solution SIEM héritée :


|Zone de préparation  |Détails  |
|---------|---------|
|**Préparation technologique**     | **Vérifiez les données critiques** : Assurez-vous que toutes les sources et alertes sont disponibles dans Azure Sentinel. <br><br>**Archivez tous les enregistrements** : Sauvegardez les enregistrements critiques d’incidents et de cas passés, données brutes facultatives, pour conserver l’historique institutionnel.   |
|**Préparation des processus**     |  **Playbooks** : Mettez à jour les [processus d’enquête et de chasse](investigate-cases.md) dans Azure Sentinel.<br><br>**Métriques** : Assurez-vous que vous pouvez obtenir toutes les métriques clés d’Azure Sentinel.<br><br>**Classeurs** : Créez des [classeurs personnalisés](monitor-your-data.md) ou utilisez des modèles de classeur intégré pour obtenir rapidement des informations dès que vous [vous connectez aux sources de données](connect-data-sources.md).<br><br>**Incidents** : Veillez à transférer tous les incidents actuels vers le nouveau système, y compris les données sources requises.        |
|**Préparation du personnel**     |  **Analystes SOC** : Assurez-vous que tous les membres de votre équipe sont formés à Azure Sentinel et qu’ils sont à l’aise pour quitter la SIEM héritée.   |
|     |         |
## <a name="next-steps"></a>Étapes suivantes

Après la migration, explorez les ressources Azure Sentinel de Microsoft pour développer vos compétences et tirer le meilleur parti d’Azure Sentinel.

Envisagez également de renforcer votre protection contre les menaces en utilisant Azure Sentinel aux côtés de [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) et [Azure Defender](../security-center/azure-defender.md) pour une [protection intégrée contre les menaces](https://www.microsoft.com/security/business/threat-protection). Tirez parti de l’étendue de la visibilité qu’offre Azure Sentinel tout en plongeant plus en détail dans l’analyse des menaces.

Pour plus d'informations, consultez les pages suivantes :

- [Meilleures pratiques de migration des règles](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [Webinaire : Meilleures pratiques pour la conversion des règles de détection](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Orchestration, automatisation et réponse aux incidents de sécurité (SOAR) dans Azure Sentinel](automation-in-azure-sentinel.md)
- [Mieux gérer votre SOC avec des métriques d’incident](manage-soc-with-incident-metrics.md)
- [Parcours d’apprentissage Azure Sentinel](/learn/paths/security-ops-sentinel/)
- [Certification SC-200 d’analyste des opérations de sécurité de Microsoft](/learn/certifications/exams/sc-200)
- [Formation Azure Sentinel, niveau Ninja](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Enquêter sur une attaque dans un environnement hybride avec Azure Sentinel](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)