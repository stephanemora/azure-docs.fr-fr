---
title: Nouveautés d’Azure Sentinel
description: Cet article décrit les nouvelles fonctionnalités d’Azure Sentinel introduites au cours des derniers mois.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 7c4a2958f8629b224cecf1e92fd0efcff6b1fdd6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533283"
---
# <a name="whats-new-in-azure-sentinel"></a>Nouveautés d’Azure Sentinel

Cet article liste les fonctionnalités récentes ajoutées à Azure Sentinel et les nouvelles fonctionnalités des services associés qui offrent une expérience utilisateur améliorée dans Azure Sentinel.

Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Sentinel](whats-new-archive.md). Pour plus d’informations sur les fonctionnalités précédentes fournies, consultez nos [blogs Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

> [!IMPORTANT]
> Les fonctionnalités indiquées sont disponibles en préversion. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Nos équipes de chasse des menaces Microsoft mettent à la disposition de la [communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) des requêtes, playbooks, workbooks et notebooks, notamment des [requêtes de chasse](https://github.com/Azure/Azure-Sentinel) spécifiques que vos équipes peuvent adapter et utiliser.
>
> Vous pouvez également contribuer ! Rejoignez-nous dans la [communauté GitHub des chasseurs de menaces Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="august-2021"></a>Août 2021

- [Recherche d’incidents avancée (préversion publique)](#advanced-incident-search-public-preview)
- [Détection de fusion des rançongiciels (préversion publique)](#fusion-detection-for-ransomware-public-preview)
- [Modèles Watchlist pour les données UEBA](#watchlist-templates-for-ueba-data-public-preview)
- [Schéma de normalisation des événements de fichier (préversion publique)](#file-event-normalization-schema-public-preview)
- [Nouveau dans la documentation : Guide des meilleures pratiques](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>Recherche d’incidents avancée (préversion publique)

Par défaut, les recherches d’incidents se font uniquement avec les valeurs **ID d’incident**, **Titre**, **Étiquettes**, **Propriétaires** et **Nom du produit**. Azure Sentinel propose désormais des [options de recherche avancée](investigate-cases.md#search-for-incidents) pour rechercher des données supplémentaires, notamment des détails sur les alertes, des descriptions, des entités, des tactiques et bien plus.

Par exemple :

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Capture d’écran des options de recherche avancée de la page Incidents.":::

Pour plus d’informations, consultez [Rechercher des incidents](investigate-cases.md#search-for-incidents).

### <a name="fusion-detection-for-ransomware-public-preview"></a>Détection de fusion des rançongiciels (préversion publique)

Azure Sentinel fournit désormais de nouvelles détections de fusion pour les éventuelles activités de rançongiciel, générant des incidents de type **Plusieurs alertes pouvant être liées à une activité de rançongiciel détectées**.

Les incidents sont générés pour les alertes éventuellement associées à des activités de rançongiciel, lorsqu’ils interviennent pendant un laps de temps spécifique, et sont associés aux étapes Exécution et Évasion défense d’une attaque. Vous pouvez utiliser les alertes répertoriées dans l’incident pour analyser les techniques éventuellement utilisées par les attaquants pour compromettre un hôte/appareil et échapper à la détection.

Parmi les connecteurs de données pris en charge figurent les suivants :

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](connect-microsoft-defender-advanced-threat-protection.md)
- [Microsoft Defender pour Identity](connect-azure-atp.md)
- [Microsoft Cloud App Security](connect-cloud-app-security.md)
- [Règles d’analyse planifiée d’Azure Sentinel](detect-threats-built-in.md#scheduled)

Pour plus d’informations, consultez [Plusieurs alertes pouvant être liées à une activité de rançongiciel détectées](fusion.md#multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview).

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>Modèles Watchlist pour les données UEBA (préversion publique)

Azure Sentinel fournit désormais des modèles Watchlist intégrés pour les données UEBA. Vous pouvez les personnaliser pour votre environnement et les utiliser lors de vos investigations.

Une fois les Watchlists UEBA renseignées, vous pouvez corréler ces données avec des règles d’analyse, les afficher dans pages d’entités et les graphiques d’investigations sous forme d’insights, créer des utilisations personnalisées pour suivre les adresses IP virtuelles ou les utilisateurs sensibles, et bien plus.

Actuellement, les modèles Watchlist incluent :

- **Utilisateurs d’adresses IP virtuelles**. Liste des comptes utilisateurs des employés ayant une valeur à fort impact au sein de l’organisation.
- **Employés congédiés**. Liste des comptes utilisateurs des employés ayant été congédiés ou en passe de l’être.
- **Comptes de service**. Liste des comptes de service et de leurs propriétaires.
- **Corrélation d’identité**. Liste des comptes utilisateurs associés appartenant à la même personne.
- **Ressources de valeur élevée**. Liste des appareils, ressources ou autres actifs ayant une valeur critique au sein de l’organisation.
- **Mappage réseau**. Liste des sous-réseaux IP et de leurs contextes organisationnels respectifs.

Pour plus d’informations, consultez [Créer une nouvelle Watchlist à l’aide d’un modèle](watchlists.md#create-a-new-watchlist-using-a-template-public-preview) et [Schémas Watchlist intégrés](watchlist-schemas.md).



### <a name="file-event-normalization-schema-public-preview"></a>Schéma de normalisation des événements de fichier (préversion publique)

Le modèle ASIM (Azure Sentinel Information Model) prend désormais en charge un schéma de normalisation des événements de fichier, qui est utilisé pour décrire l’activité des fichiers, comme la création, la modification ou la suppression de fichiers ou de documents. Ces événements sont signalés par les systèmes d’exploitation, les systèmes de stockage de fichiers tels qu’Azure Files et les systèmes de gestion de documents tels que Microsoft SharePoint.

Pour plus d'informations, consultez les pages suivantes :

- [Référence de schéma de normalisation d’événement du fichier Azure Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>Nouveau dans la documentation : Guide des meilleures pratiques

En réponse à plusieurs demandes de nos clients et équipes de support technique, nous avons ajouté une série de conseils en matière de meilleures pratiques à notre documentation.

Pour plus d'informations, consultez les pages suivantes :

- [Prérequis pour le déploiement d’Azure Sentinel](prerequisites.md)
- [Meilleures pratiques pour Azure Sentinel](best-practices.md)
- [Meilleures pratiques pour l’architecture de l’espace de travail Azure Sentinel](best-practices-workspace-architecture.md)
- [Concevoir votre architecture d’espace de travail Azure Sentinel](design-your-workspace-architecture.md)
- [Exemples de conceptions d’espaces de travail Azure Sentinel](sample-workspace-designs.md)
- [Meilleures pratiques de collecte de données](best-practices-data.md)

> [!TIP]
> Pour plus d’informations, consultez notre documentation sur les articles conceptuels et procédures pertinents. Pour plus d’informations, consultez [Références supplémentaires en matière de meilleures pratiques](best-practices.md#additional-best-practice-references).
>

## <a name="july-2021"></a>Juillet 2021

- [Analyse de correspondance Microsoft Threat Intelligence (préversion publique)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Utiliser des données Azure AD avec la table IdentityInfo d’Azure Sentinel (préversion publique)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [Enrichir les entités avec des données de géolocalisation via l’API (préversion publique)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [Prise en charge des requêtes de ressources croisées ADX (préversion publique)](#support-for-adx-cross-resource-queries-public-preview)
- [Watchlists en disponibilité générale](#watchlists-are-in-general-availability)
- [Prise en charge de la résidence des données dans plus de zones géographiques](#support-for-data-residency-in-more-geos)
- [Synchronisation bidirectionnelle dans le connecteur Azure Defender (préversion publique)](#bidirectional-sync-in-azure-defender-connector-public-preview)


### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Analyse de correspondance Microsoft Threat Intelligence (préversion publique)

Azure Sentinel fournit désormais la règle intégrée **Analyse de correspondance Microsoft Threat Intelligence** qui met en correspondance les données de renseignement sur les menaces générées par Microsoft avec vos journaux. Cette règle génère des alertes et des incidents haute fidélité, avec des gravités appropriées en fonction du contexte des journaux détectés. Lorsqu’une correspondance est détectée, l’indicateur est également publié dans votre référentiel de renseignement sur les menaces Azure Sentinel.

La règle **Analyse de correspondance Microsoft Threat Intelligence** met en correspondance des indicateurs de domaine avec les sources de journaux suivantes :

- [CEF](connect-common-event-format.md)
- [DNS](connect-dns.md)
- [Syslog](connect-syslog.md)

Pour plus d’informations, consultez [Détecter des menaces à l’aide de l’analyse de correspondance (préversion publique)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview).

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Utiliser des données Azure AD avec la table IdentityInfo d’Azure Sentinel (préversion publique)

Les attaquants utilisant souvent les propres comptes d’utilisateur et de service de l’organisation, les données relatives à ces comptes d’utilisateur, notamment les informations d’identification et les privilèges de l’utilisateur, sont essentielles pour les analystes dans le processus d’investigation.

Désormais, l'[activation d’UEBA](enable-entity-behavior-analytics.md)dans votre espace de travail Azure Sentinel synchronise également les données Azure AD dans la nouvelle table **IdentityInfo** de Log Analytics. Les synchronisations entre votre instance Azure AD et la **IdentityInfo** créent un instantané de vos données de profil utilisateur incluant les métadonnées utilisateur, les informations de groupe et les rôles Azure AD attribués à chaque utilisateur.

Utilisez la table **IdentityInfo** lorsque vous menez des investigations et affinez les règles d’analyse de votre organisation afin de réduire le nombre de faux positifs.

Pour plus d’informations, consultez [Table IdentityInfo](ueba-enrichments.md#identityinfo-table-public-preview) dans la documentation de référence sur les enrichissements UEBA et [Utiliser les données UEBA pour analyser les faux positifs](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives).

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>Enrichir les entités avec des données de géolocalisation via l’API (préversion publique)

Azure Sentinel propose désormais une API pour enrichir vos données avec les informations de géolocalisation. Les données de géolocalisation peuvent ensuite être utilisées pour analyser et examiner les incidents de sécurité.

Pour plus d’informations, consultez [Enrichir des entités dans Azure Sentinel avec des données de géolocalisation via l’API REST (préversion publique)](geolocation-data-api.md) et [Classer et analyser les données à l’aide d’entités dans Azure Sentinel](entities-in-azure-sentinel.md).


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>Prise en charge des requêtes de ressources croisées ADX (préversion publique)

Dans Azure Sentinel, l’expérience de chasse prend désormais en charge les [requêtes de ressources croisées ADX](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).
 
Bien que Log Analytics reste l’emplacement de stockage de données principal pour effectuer des analyses avec Azure Sentinel, ADX est parfois requis pour stocker des données en raison de coûts, de périodes de rétention ou d’autres facteurs. Cette fonctionnalité permet aux clients de parcourir un plus vaste ensemble de données et d’afficher les résultats dans les [expériences de chasse Azure Sentinel](hunting.md), y compris les requêtes de chasse, [Livestream](livestream.md) et la page de recherche Log Analytics.

Pour interroger les données stockées dans les clusters ADX, utilisez la fonction adx() pour spécifier le cluster ADX, le nom de la base de données et la table souhaitée. Vous pouvez ensuite interroger la sortie comme n’importe quelle autre table. Pour plus d’informations, consultez les pages liées ci-dessus.




### <a name="watchlists-are-in-general-availability"></a>Watchlists en disponibilité générale

La fonctionnalité [Watchlists](watchlists.md) est désormais en disponibilité générale. Utilisez les Watchlists pour enrichir les alertes avec des données métier, créer des listes d’autorisation et de refus lesquelles vérifier les événements d’accès, faciliter l’examen des menaces et réduire la charge de travail liée aux alertes.

### <a name="support-for-data-residency-in-more-geos"></a>Prise en charge de la résidence des données dans plus de zones géographiques

Azure Sentinel prend désormais en charge la résidence des données dans les zones géographiques supplémentaires suivantes :

Brésil, Norvège, Afrique du Sud, Corée, Allemagne, Émirats Arabes Unis et Suisse.

Pour plus d’informations sur la résidence des données, consultez la [liste complète des zones géographiques prises en charge](quickstart-onboard.md#geographical-availability-and-data-residency).

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Synchronisation bidirectionnelle dans le connecteur Azure Defender (préversion publique)

Le connecteur Azure Defender prend désormais en charge la synchronisation bidirectionnelle de l’état des alertes entre Defender et Azure Sentinel. Lorsque vous fermez un incident Sentinel contenant une alerte Defender, l’alerte est automatiquement fermée dans le portail Defender.

Consultez cette [description complète du connecteur Azure Defender mis à jour](connect-azure-security-center.md).

## <a name="june-2021"></a>Juin 2021

- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [Connecteurs de service à service mis à jour](#updated-service-to-service-connectors)
- [Exporter et importer des règles d’analyse (préversion publique)](#export-and-import-analytics-rules-public-preview)
- [Enrichissement des alertes : détails des alertes (préversion publique)](#alert-enrichment-alert-details-public-preview)
- [Aide supplémentaire pour les playbooks](#more-help-for-playbooks)
- [Nouvelle réorganisation de la documentation](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>Normalisation et modèle ASIM (Azure Sentinel Information Model)

Le modèle ASIM (Azure Sentinel Information Model) vous permet d’utiliser et de créer du contenu indépendant de la source, ce qui simplifie l’analyse des données dans votre espace de travail Azure Sentinel.

Dans le cadre de la mise à jour de ce mois-ci, nous avons amélioré notre documentation de normalisation, en fournissant de nouveaux niveaux de données DNS, d’événements de processus et de schémas de normalisation de l’authentification.

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md) (mise à jour)
- [Référence sur le schéma de normalisation de l’authentification Azure Sentinel (préversion publique)](authentication-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation des données Azure Sentinel](normalization-schema.md)
- [Référence de schéma de normalisation DNS Azure Sentinel (préversion publique)](dns-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation des événements de processus Azure Sentinel (préversion publique)](process-events-normalization-schema.md) (nouveau)
- [Référence de schéma de normalisation d’événement du registre Azure Sentinel (préversion publique)](registry-event-normalization-schema.md) (nouveau)


### <a name="updated-service-to-service-connectors"></a>Connecteurs de service à service mis à jour

Deux de nos connecteurs les plus utilisés ont bénéficié de mises à niveau majeures.

- Le [connecteur d’événements de sécurité Windows (préversion publique)](connect-windows-security-events.md) est désormais basé sur le nouvel agent Azure Monitor (AMA), ce qui vous permet de choisir les données à ingérer et de bénéficier d’une visibilité maximale à moindre coût.

- Le [connecteur des journaux d’activité Azure](connect-azure-activity.md) est désormais basé sur le pipeline des paramètres de diagnostic, ce qui vous permet d’obtenir des données plus complètes, de réduire la latence d’ingestion et de bénéficier de meilleures performances et d’une fiabilité accrue.

Les mises à niveau ne sont pas automatiques. Les utilisateurs de ces connecteurs sont encouragés à activer les nouvelles versions.

### <a name="export-and-import-analytics-rules-public-preview"></a>Exporter et importer des règles d’analyse (préversion publique)

Vous pouvez désormais exporter vos règles d’analyse vers des fichiers modèles ARM (Azure Resource Manager) au format JSON et importer des règles à partir de ces fichiers dans le cadre de la gestion et du contrôle de vos déploiements Azure Sentinel en tant que code. Tout type de [règle d’analyse](detect-threats-built-in.md), pas uniquement les règles **planifiées**, peut être exporté vers un modèle ARM. Le fichier de modèle comprend toutes les informations de la règle, de sa requête aux tactiques MITRE ATT&CK qui lui sont attribuées.

Pour plus d’informations, consultez [Exporter et importer des règles analytiques vers et depuis des modèles ARM](import-export-analytics-rules.md).

### <a name="alert-enrichment-alert-details-public-preview"></a>Enrichissement des alertes : détails des alertes (préversion publique)

Outre l’enrichissement de votre contenu d’alerte avec les détails personnalisés et le mappage d’entités, vous pouvez désormais personnaliser la façon dont les alertes (et par extension, les incidents) sont présentées et affichées en fonction de leur contenu particulier. À l’instar des autres fonctionnalités d’enrichissement des alertes, cela peut être configuré dans l’[Assistant règle d’analyse](detect-threats-custom.md).

Pour plus d’informations, consultez [Personnaliser les détails des alertes dans Azure Sentinel](customize-alert-details.md).


### <a name="more-help-for-playbooks"></a>Aide supplémentaire pour les playbooks

Deux nouveaux documents peuvent vous aider à vous familiariser avec la création et l’utilisation de playbooks.
- [Authentifier les playbooks auprès d’Azure Sentinel](authenticate-playbooks-to-sentinel.md) vous aide à comprendre les différentes méthodes d’authentification grâce auxquelles les règles basées sur Logic Apps peuvent se connecter aux informations et y accéder dans Azure Sentinel, ainsi que le moment opportun pour les utiliser.
- [Utiliser des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md) explique la différence entre le **déclencheur d’incident** et le **déclencheur d’alerte**, ainsi que les différentes actions que vous pouvez effectuer dans les playbooks pour répondre aux incidents, y compris comment accéder aux informations dans les [détails personnalisés](playbook-triggers-actions.md#work-with-custom-details).

La documentation relative aux playbooks aborde également explicitement le scénario MSSP mutualisé.

### <a name="new-documentation-reorganization"></a>Nouvelle réorganisation de la documentation

Ce mois-ci, nous avons réorganisé notre [documentation Azure Sentinel](index.yml), en regroupant les catégories intuitives qui suivent les parcours courants des clients. Utilisez la page d’accueil de la recherche de documents filtrés et mis à jour pour naviguer dans la documentation Azure Sentinel.

:::image type="content" source="media/whats-new/new-docs.png" alt-text="Nouvelle réorganisation de la documentation Azure Sentinel." lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>Mai 2021

- [Module Azure Sentinel PowerShell](#azure-sentinel-powershell-module)
- [Améliorations du regroupement des alertes](#alert-grouping-enhancements)
- [Solutions Azure Sentinel (préversion publique)](#azure-sentinel-solutions-public-preview)
- [Solution Continuous Threat Monitoring pour SAP (préversion publique)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [Intégrations Threat Intelligence (préversion publique)](#threat-intelligence-integrations-public-preview)
- [Fusion sur les alertes planifiées (préversion publique)](#fusion-over-scheduled-alerts-public-preview)
- [Anomalies SOC-ML (préversion publique)](#soc-ml-anomalies-public-preview)
- [Page Entité IP (préversion publique)](#ip-entity-page-public-preview)
- [Personnalisation d’activité (préversion publique)](#activity-customization-public-preview)
- [Tableau de bord de repérage (préversion publique)](#hunting-dashboard-public-preview)
- [Équipe d’incident - Collaborer dans Microsoft Teams (préversion publique)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [Classeur Confiance Zéro (TIC 3.0)](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Module Azure Sentinel PowerShell

Le module officiel Azure Sentinel PowerShell permettant d’automatiser les tâches opérationnelles quotidiennes est en disponibilité générale.

Vous pouvez le télécharger ici : [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Pour plus d’informations, consultez la documentation PowerShell : [Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="alert-grouping-enhancements"></a>Améliorations du regroupement des alertes

Vous pouvez maintenant configurer votre règle d’analyse pour regrouper les alertes dans un seul incident, non seulement lorsqu’elles correspondent à un type d’entité spécifique, mais également lorsqu’elles correspondent à un nom d’alerte spécifique, à une gravité ou à d’autres détails personnalisés pour une entité configurée. 

Dans l’onglet **Paramètres des incidents** de l’Assistant règle d’analyse, sélectionnez cette option pour activer le regroupement des alertes, puis sélectionnez l’option **Regrouper les alertes dans un seul incident si les types d’entités et les détails correspondent**. 

Sélectionnez ensuite le type d’entité et les détails que vous souhaitez faire correspondre :

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="Regrouper les alertes en mettant en correspondant les détails d’entité.":::

Pour plus d'informations, consultez [Regroupement des alertes](detect-threats-custom.md#alert-grouping).

### <a name="azure-sentinel-solutions-public-preview"></a>Solutions Azure Sentinel (préversion publique)

Azure Sentinel offre désormais des [solutions](sentinel-solutions-catalog.md) à **contenu packagé** qui incluent des combinaisons d’un ou plusieurs connecteurs de données, classeurs, règles d’analyse, playbooks, requêtes de chasse, analyseurs, watchlists et d’autres composants pour Azure Sentinel.

Les solutions améliorent la détectabilité dans les produits, le déploiement en une seule étape et des scénarios de produit de bout en bout. Pour plus d’informations, consultez [Découvrir et déployer des solutions Azure Sentinel](sentinel-solutions-deploy.md).

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Solution Continuous Threat Monitoring pour SAP (préversion publique)

Les solutions Azure Sentinel incluent désormais **Continuous Threat Monitoring pour SAP**, ce qui vous permet de superviser les systèmes SAP à la recherche des menaces sophistiquées au sein des couches métier et d’application.

Le connecteur de données SAP diffuse 14 journaux d’application de l’ensemble du paysage du système SAP, et collecte des journaux tant à partir du langage de programmation ABAP (Advanced Business Application Programming) via des appels RFC NetWeaver, qu’à partir des données de stockage de fichiers via l’interface de contrôle OSSAP. Le connecteur de données SAP ajoute à Azure Sentinel la capacité de surveiller l’infrastructure SAP sous-jacente.

Pour ingérer des journaux SAP dans Azure Sentinel, le connecteur de données SAP Sentinel Azure doit être installé sur votre environnement SAP. Une fois le connecteur de données SAP déployé, déployez le contenu riche de sécurité de la solution SAP pour avoir un aperçu de l’environnement SAP de votre organisation et améliorer les fonctionnalités d’opération de sécurité associées.

Pour plus d’informations, consultez [Tutoriel : Déployer la solution Azure Sentinel pour SAP (préversion publique)](sap-deploy-solution.md).

### <a name="threat-intelligence-integrations-public-preview"></a>Intégrations Threat Intelligence (préversion publique)

Azure Sentinel vous offre différentes façons d’utiliser des flux de [renseignement sur les menaces](./understand-threat-intelligence.md) pour améliorer la capacité des analystes de la sécurité à détecter et hiérarchiser les menaces connues.

Vous pouvez maintenant utiliser l’un des nombreux nouveaux produits de la plateforme décisionnelle intégrée (TIP) disponibles, vous connecter aux serveurs TAXII pour tirer parti de toute source de renseignement sur les menaces compatible avec STIX, et également utiliser les solutions personnalisées qui peuvent communiquer directement avec [l’API Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator).

Vous pouvez également vous connecter à des sources de renseignement sur les menaces à partir de playbooks, afin d’enrichir les incidents avec des informations IT qui peuvent aider à effectuer des investigations et à exécuter des actions de réponse.

Pour plus d’informations, consultez [Intégration Threat Intelligence dans Azure Sentinel](threat-intelligence-integration.md).

### <a name="fusion-over-scheduled-alerts-public-preview"></a>Fusion sur les alertes planifiées (préversion publique)

Le moteur de corrélation de Machine Learning **Fusion** peut désormais détecter les attaques multiphases à l’aide d’alertes générées par un ensemble de [règles analytiques planifiées](detect-threats-custom.md) dans ses corrélations, en plus des alertes importées à partir d’autres sources de données.

Pour plus d’informations, consultez [Détection avancée des attaques multiphases dans Azure Sentinel](fusion.md).

### <a name="soc-ml-anomalies-public-preview"></a>Anomalies SOC-ML (préversion publique)

Les anomalies basées sur le Machine Learning SOC-ML d’Azure Sentinel peuvent identifier un comportement inhabituel susceptible d’échapper à la détection.

SOC-ML utilise des modèles de règle analytique qui peuvent être mis en place et fonctionner immédiatement. Même si les anomalies n’indiquent pas nécessairement un comportement malveillant ou suspect, elles peuvent être utilisées pour améliorer la fidélité des détections, des investigations et de la recherche de menaces.

Pour plus d’informations, consultez [Utiliser des anomalies SOC-ML pour détecter les menaces dans Azure Sentinel](soc-ml-anomalies.md).

### <a name="ip-entity-page-public-preview"></a>Page Entité IP (préversion publique)

Azure Sentinel prend désormais en charge l’entité d’adresse IP, et vous pouvez désormais afficher les informations d’entité IP dans la nouvelle page Entité IP.

À l’instar des pages d’entité utilisateur et hôte, la page IP contient des informations générales sur l’adresse IP, une liste des activités auxquelles l’adresse IP a participé, et bien plus encore, vous offrant une banque d’informations encore plus riche pour améliorer votre investigation des incidents de sécurité.

Pour plus d’informations, consultez [Pages d’entité](identify-threats-with-entity-behavior-analytics.md#entity-pages).

### <a name="activity-customization-public-preview"></a>Personnalisation d’activité (préversion publique)

Concernant les pages d’entité, vous pouvez désormais créer des activités personnalisées pour vos entités, qui seront suivies et affichées sur leurs pages d’entité respectives, en plus des activités prêtes à l’emploi dont vous disposez déjà.

Pour plus d’informations, consultez [Personnaliser les activités sur les chronologies des pages d’entité](customize-entity-activities.md).

### <a name="hunting-dashboard-public-preview"></a>Tableau de bord de repérage (préversion publique)

Le panneau **Hunting** (Repérage) a été actualisé. Le nouveau tableau de bord vous permet d’exécuter toutes vos requêtes, ou un sous-ensemble sélectionné, en un seul clic.

Identifiez où commencer le repérage en examinant le nombre de résultats, les pics ou la modification du nombre de résultats sur une période de 24 heures. Vous pouvez également trier et filtrer par favoris, source de données, tactique et technique MITRE ATT&CK, résultats ou delta de résultat. Affichez les requêtes qui n’ont pas encore les sources de données nécessaires connectées et recevez des recommandations sur la façon d’activer ces requêtes.

Pour plus d’informations, consultez [Repérer les menaces avec Azure Sentinel](hunting.md).

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Équipe d’incident - Azure Sentinel - Collaborer avec Microsoft Teams (préversion publique)

Azure Sentinel prend désormais en charge une intégration directe avec Microsoft Teams, ce qui vous permet de collaborer en toute transparence au sein de l’organisation et avec des parties prenantes externes.

Directement à partir de l’incident dans Azure Sentinel, créez une nouvelle *équipe d’incident* à utiliser pour la communication et la coordination centralisées.

Les équipes d’incident sont particulièrement utiles lorsqu’elles sont utilisées en tant que pont de conférence dédié pour les incidents à gravité élevée en cours. Les organisations qui utilisent déjà Microsoft Teams pour la communication et la collaboration peuvent utiliser l’intégration Azure Sentinel pour intégrer des données de sécurité directement dans leurs conversations et leurs tâches quotidiennes.

Dans Microsoft Teams, l’onglet **page Incident** de la nouvelle équipe contient toujours les données les plus récentes et à jour d’Azure Sentinel, garantissant ainsi que les données les plus pertinentes sont disponibles pour vos équipes.

[ ![Page Incident dans Microsoft Teams.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Pour plus d’informations, consultez [Collaborer dans Microsoft Teams (préversion publique)](collaborate-in-microsoft-teams.md).

### <a name="zero-trust-tic30-workbook"></a>Classeur Confiance Zéro (TIC 3.0)

Le nouveau classeur Confiance Zéro (TIC3.0) d’Azure Sentinel fournit une visualisation automatisée des principes de [Confiance Zéro](/security/zero-trust/) croisée avec l’infrastructure [Trusted Internet Connections](https://www.cisa.gov/trusted-internet-connections) (TIC).

Nous savons que la conformité n’est pas seulement une exigence annuelle et que les organisations doivent surveiller les configurations au fil du temps. Le classeur Confiance Zéro d’Azure Sentinel utilise toute la gamme des offres de sécurité Microsoft dans Azure, Office 365, Teams, Intune, Windows Virtual Desktop et bien d’autres.

[ ![Classeur Confiance Zéro. ](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**Le classeur Confiance Zéro** :

- Permet aux implémenteurs, analystes des opérations de sécurité, évaluateurs, décideurs de sécurité et de conformité, MSSP et autres d’avoir connaissance de la posture de sécurité des charges de travail cloud.
- Propose plus de 75 cartes de contrôle, alignées sur les fonctionnalités de sécurité TIC 3.0, avec des boutons d’interface utilisateur sélectionnables pour la navigation.
- Est conçu pour habiliter le personnel grâce à l’automatisation, l’intelligence artificielle, le Machine Learning, la génération de requêtes/alertes, les visualisations, les recommandations personnalisées et les références de documentation respectives.

Pour plus d’informations, consultez [Visualiser et superviser vos données](monitor-your-data.md).

## <a name="april-2021"></a>Avril 2021

- [Connecteurs de données basés sur Azure Policy](#azure-policy-based-data-connectors)
- [Chronologie des incidents (préversion publique)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Connecteurs de données basés sur Azure Policy

Azure Policy vous permet d'appliquer un ensemble commun de paramètres de journaux de diagnostic à toutes les ressources (actuelles et futures) d'un type particulier dont vous souhaitez ingérer les journaux dans Azure Sentinel.

Dans le cadre des efforts que nous déployons pour mettre la puissance d'[Azure Policy](../governance/policy/overview.md) au service de la configuration de la collecte de données, nous proposons désormais en préversion publique un autre collecteur de données basé sur Azure Policy pour les [comptes de stockage Azure](connect-azure-storage-account.md).

En outre, deux de nos connecteurs qui étaient jusque-là en préversion, pour [Azure Key Vault](connect-azure-key-vault.md) et [Azure Kubernetes Service](connect-azure-kubernetes-service.md), sont désormais en disponibilité générale (GA), comme le connecteur [Azure SQL Database](connect-azure-sql-logs.md) qui les a précédé.

### <a name="incident-timeline-public-preview"></a>Chronologie des incidents (préversion publique)

Le premier onglet d’une page de détails d’incident est maintenant **Chronologie**, qui affiche une chronologie des alertes et des signets dans l’incident. La chronologie d’un incident peut vous aider à mieux comprendre l’incident et à retracer la chronologie de l’activité de l’attaquant parmi les alertes et les signets associés.

- Sélectionnez un élément dans la chronologie pour afficher ses détails, sans quitter le contexte de l’incident
- Filtrez le contenu de la chronologie pour afficher uniquement les alertes ou les signets, ou les éléments d’une gravité ou d’une tactique MITRE spécifique.
- Vous pouvez sélectionner le lien **ID d’alerte système** pour afficher l’intégralité de l’enregistrement, ou le lien **Événements** pour afficher les événements associés dans la zone **Journaux**.

Par exemple :

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Onglet Chronologie de l’incident":::

Pour plus d’informations, consultez [Didacticiel : Examiner les incidents avec Azure Sentinel](investigate-cases.md).

## <a name="march-2021"></a>Mars 2021

- [Définir les classeurs pour qu’ils s’actualisent automatiquement en mode affichage](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nouvelles détections pour le pare-feu Azure](#new-detections-for-azure-firewall)
- [Règles d'automatisation et guides opérationnels déclenchés par un incident (préversion publique)](#automation-rules-and-incident-triggered-playbooks-public-preview) (y compris la toute nouvelle documentation des guides opérationnels)
- [Nouveaux enrichissements d'alerte : mappage d'entités amélioré et détails personnalisés (préversion publique)](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [Imprimer vos classeurs Azure Sentinel ou enregistrer au format PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtres d’incident et préférences de tri désormais enregistrés dans votre session (préversion publique)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Intégration des incidents Microsoft 365 Defender (préversion publique)](#microsoft-365-defender-incident-integration-public-preview)
- [Nouveaux connecteurs de service Microsoft avec Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Définir les classeurs pour qu’ils s’actualisent automatiquement en mode affichage

Les utilisateurs d’Azure Sentinel peuvent désormais utiliser la nouvelle [fonctionnalité d’Azure Monitor](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) pour actualiser automatiquement les données de classeur pendant une session d’affichage.

Dans chaque classeur ou modèle de classeur, sélectionnez :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Actualisation automatique** pour afficher vos options d’intervalle. Sélectionnez l’option que vous souhaitez utiliser pour la session d’affichage en cours, puis **Appliquer**.

- Les intervalles d’actualisation pris en charge sont compris entre **5 minutes** et **1 jour**.
- Par défaut, l’actualisation automatique est désactivée. Pour optimiser les performances, l’actualisation automatique est également désactivée chaque fois que vous fermez un classeur, et elle ne s’exécute pas en arrière-plan. Activez l’actualisation automatique en fonction des besoins la prochaine fois que vous ouvrirez le classeur.
- L’actualisation automatique est suspendue lorsque vous modifiez un classeur, et les intervalles d’actualisation automatique sont redémarrés chaque fois que vous revenez en mode d’affichage à partir du mode d’édition.

    Les intervalles sont également redémarrés si vous actualisez manuellement le classeur en sélectionnant le bouton :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Actualiser**.

Pour plus d’informations, consultez [Visualiser et surveiller vos données](monitor-your-data.md) et la [documentation d’Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nouvelles détections pour le pare-feu Azure

Plusieurs détections prêtes à l’emploi pour le pare-feu Azure ont été ajoutées à la zone [Analytique](./understand-threat-intelligence.md) dans Azure Sentinel. Ces nouvelles détections permettent aux équipes de sécurité d’obtenir des alertes si des machines sur le réseau interne tentent d’interroger ou de se connecter à des noms de domaine Internet ou à des adresses IP associées à des IOC connus, comme défini dans la requête de règle de détection.

Les nouvelles détections sont les suivantes :

- [Balise réseau Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Domaines et hachages GALLIUM connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Known IRIDIUM IP (Adresse IP IRIDIUM connue)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Domaines/adresses IP de groupe Phosphorus connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domaines THALLIUM inclus dans DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Hachages maldoc liés à ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Domaines de groupe STRONTIUM connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - Domaine et IOC IP - Mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Les détections pour les pare-feu Azure sont ajoutées en permanence à la galerie de modèles intégrée. Pour obtenir les détections les plus récentes pour le pare-feu Azure, sous **Modèles de règle**, filtrez les **Sources de données** par **Pare-feu Azure** :

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nouvelles détections dans le classeur d’efficacité analytique":::

Pour plus d’informations, consultez [Nouvelles détections pour le pare-feu Azure dans Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>Règles d'automatisation et guides opérationnels déclenchés par un incident (préversion publique)

Les règles d’automatisation sont un nouveau concept dans Azure Sentinel, qui vous permet de gérer de manière centralisée l’automatisation de la gestion des incidents. En plus de vous permettre d’affecter des guides opérationnels aux incidents (pas seulement aux alertes comme auparavant), les règles d’automatisation vous permettent également d’automatiser les réponses pour plusieurs règles d’analyse à la fois, d’étiqueter, d’attribuer ou de fermer automatiquement des incidents sans nécessiter de guides opérationnels et de contrôler l’ordre des actions exécutées. Les règles d’automatisation simplifient l’utilisation de l’automatisation dans Azure Sentinel et vous permettent de simplifier des flux de travail complexes pour vos processus d’orchestration d’incident.

Pour en savoir plus, consultez cette [explication complète sur les règles d’automatisation](automate-incident-handling-with-automation-rules.md).

Comme indiqué ci-dessus, les règles peuvent désormais être activées avec le déclencheur d’incident en plus du déclencheur d’alerte. Le déclencheur d’incident fournit à vos guides opérationnels un plus grand ensemble d’entrées à utiliser (puisque l’incident inclut également toutes les données d’alerte et d’entité), ce qui vous donne encore plus de puissance et de flexibilité dans vos flux de travail de réponse. Les règles déclenchées par incident sont activées par l’appel à partir de règles d’automatisation.

En savoir plus sur les [fonctionnalités améliorées des guides opérationnels](automate-responses-with-playbooks.md) et sur la [création d’un flux de travail de réponse](tutorial-respond-threats-playbook.md) à l’aide de guides opérationnels avec des règles d’automatisation.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>Nouveaux enrichissements d'alerte : mappage d'entités amélioré et détails personnalisés (préversion publique)

Enrichissez vos alertes de deux façons pour les rendre plus utilisables et plus instructifs.

Commencez par mettre le mappage de votre entité au niveau suivant. Vous pouvez désormais mapper presque 20 types d’entités, des utilisateurs, des hôtes et des adresses IP, aux fichiers et aux processus, aux boîtes aux lettres, aux ressources Azure et aux appareils IoT. Vous pouvez également utiliser plusieurs identificateurs pour chaque entité, afin de renforcer leur identification unique. Vous bénéficiez ainsi d’un jeu de données beaucoup plus riche dans vos incidents, en fournissant une corrélation plus large et une investigation plus puissante. [Découvrez la nouvelle façon de mapper des entités](map-data-fields-to-entities.md) dans vos alertes.

[Découvrez-en plus sur les entités](entities-in-azure-sentinel.md) et consultez la [liste complète des entités disponibles et de leurs identificateurs](entities-reference.md).

Stimulez davantage vos capacités d’investigation et de réponse en personnalisant vos alertes en détails de surface à partir de vos événements bruts. Améliorez la visibilité du contenu des événements dans vos incidents, en vous donnant une puissance et une flexibilité accrues pour répondre aux menaces de sécurité et les examiner. [Découvrez comment faire apparaître des détails personnalisés](surface-custom-details-in-alerts.md) dans vos alertes.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Imprimer vos classeurs Azure Sentinel ou enregistrer-les au format PDF

Vous pouvez désormais imprimer des classeurs Azure Sentinel, ce qui vous permet également de les exporter vers des fichiers PDF et de les enregistrer localement ou de les partager.

Dans votre classeur, sélectionnez le menu options > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimer le contenu**. Sélectionnez ensuite votre imprimante ou sélectionnez **Enregistrer au format PDF** si nécessaire.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Imprimez votre classeur ou enregistrez-le au format PDF.":::

Pour plus d’informations, consultez [Visualiser et superviser vos données](monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtres d’incident et préférences de tri désormais enregistrés dans votre session (préversion publique)

À présent, vos filtres et tris d’incident sont enregistrés dans toute votre session Azure Sentinel, même lors de la navigation vers d’autres zones du produit.
Tant que vous êtes toujours dans la même session, le fait de revenir à la zone [Incidents](investigate-cases.md) dans Azure Sentinel affiche vos filtres et le tri comme vous l’avez laissé.

> [!NOTE]
> Les filtres d’incidents et le tri ne sont pas enregistrés après avoir quitté Azure Sentinel ou après avoir actualisé votre navigateur.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Intégration des incidents Microsoft 365 Defender (préversion publique)

L’intégration d’incidents [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) d’Azure Sentinel vous permet de diffuser tous les incidents M365D dans Azure Sentinel et de les garder synchronisés entre les deux portails. Les incidents de M365D (anciennement Protection Microsoft contre les menaces) incluent l’ensemble des alertes, entités et informations pertinentes associées, ce qui vous offre suffisamment de contexte pour effectuer un triage et une investigation préliminaire dans Azure Sentinel. Une fois dans Sentinel, les incidents restent synchronisés de manière bidirectionnelle avec M365D, ce qui vous permet de tirer parti des avantages des deux portails dans votre investigation sur l’incident.

L’utilisation conjointe d’Azure Sentinel et de Microsoft 365 Defender vous offre le meilleur des deux mondes. Vous bénéficiez de tout l’éventail des insights offert par SIEM pour l’ensemble des ressources d’informations de votre organisation, ainsi que de toute la puissance d’investigation personnalisée qu’une solution XDR procure pour protéger vos ressources Microsoft 365, tout ceci coordonné et synchronisé pour des opérations SOC fluides.

Pour plus d’informations, consultez [Intégration de Microsoft 365 Defender avec Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nouveaux connecteurs de service Microsoft avec Azure Policy

[Azure Policy](../governance/policy/overview.md) est un service Azure qui vous permet d’utiliser des stratégies pour appliquer et contrôler les propriétés d’une ressource. L’utilisation de stratégies garantit que les ressources restent conformes à vos normes de gouvernance informatique.

Parmi les propriétés des ressources qui peuvent être contrôlées par les stratégies, citons la création et la gestion des journaux de diagnostic et d’audit. Azure Sentinel utilise désormais Azure Policy pour vous permettre d’appliquer un ensemble commun de paramètres de journaux de diagnostic à toutes les ressources (actuelles et futures) d’un type particulier dont vous souhaitez ingérer les journaux dans Azure Sentinel. Grâce à Azure Policy, vous n’avez plus besoin de définir les paramètres des journaux de diagnostics ressource par ressource.

Des connecteurs basés sur Azure Policy sont maintenant disponibles pour les services Azure suivants :
- [Azure Key Vault](connect-azure-key-vault.md) (préversion publique)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (préversion publique)
- [Serveurs/bases de données Azure SQL](connect-azure-sql-logs.md) (GA)

Les clients pourront toujours envoyer les journaux manuellement pour des instances spécifiques, et ne sont pas obligés d’utiliser le moteur de stratégie.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Intégrer Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenir une visibilité des alertes](get-visibility.md)