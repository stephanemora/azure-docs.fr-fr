---
title: Coûts et facturation d’Azure Sentinel | Microsoft Docs
description: En savoir plus sur le modèle de tarification d’Azure Sentinel, l’estimation et la gestion des coûts d’Azure Sentinel et la compréhension de vos coûts et de votre facture.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/03/2021
ms.openlocfilehash: 0d9c016741fcdb36a2803eaf9705e9dc2ea1782b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950298"
---
# <a name="azure-sentinel-costs-and-billing"></a>Coûts et facturation d’Azure Sentinel

Azure Sentinel utilise un langage de requête étendu pour analyser d’énormes volumes de données opérationnelles, interagir avec elles et en tirer des insights en quelques secondes. Azure Sentinel stocke ses données à des fins d’analyse dans les espaces de travail Log Analytics d’Azure Monitor.

Lorsqu’elle est activée sur un espace de travail Log Analytics, la solution Azure Sentinel analyse automatiquement toutes les données ingérées par l’espace de travail et facture le volume de données que l’espace de travail ingère et stocke. Cet article explique comment surveiller et comprendre l’utilisation et les coûts d’Azure Sentinel et des espaces de travail Log Analytics associés et comment réaliser des économies.

## <a name="azure-sentinel-pricing-model"></a>Modèle de tarification d’Azure Sentinel

Azure Sentinel offre un modèle de tarification flexible et prévisible. Pour plus d’informations, consultez la [page de tarification d’Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/). Pour connaître les frais connexes de Log Analytics, consultez [Tarification Azure Monitor pour Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

### <a name="pay-as-you-go-and-commitment-tiers"></a>Paiement à l’utilisation et niveaux d’engagement

Il existe deux façons de payer pour le service Azure Sentinel : le paiement à l’utilisation et les niveaux d’engagement.

Paiement à l’utilisation est le modèle par défaut, basé sur le volume réel de données stockées et, éventuellement, sur la conservation des données au-delà de 90 jours. Le volume de données est mesuré en Go (10^9 octets).

Log Analytics et Azure Sentinel proposent également une tarification par niveau d’engagement, anciennement appelée Réservations de capacité, qui est plus prévisible et permet d’économiser jusqu’à 65 % par rapport au tarif de Paiement à l’utilisation. Avec la tarification par niveau d’engagement, vous pouvez acheter un engagement à partir de 100 Go/jour. Toute utilisation dépassant le niveau d’engagement est facturée au tarif du niveau d’engagement que vous avez sélectionné. Par exemple, un niveau d’engagement de 100 Go/jour vous facture le volume de données engagé de 100 Go/jour, plus tout Go/jour supplémentaire au tarif réduit pour ce niveau.

Vous pouvez augmenter votre niveau d’engagement à tout moment et le réduire tous les 31 jours, afin d’optimiser les coûts à mesure que votre volume de données augmente ou diminue. Pour voir votre niveau tarifaire Azure Sentinel actuel, sélectionnez **Paramètres** dans le volet de navigation gauche d’Azure Sentinel, puis sélectionnez l’onglet **Tarification**. Votre niveau tarifaire actuel est marqué **Niveau actuel**.

Pour définir et modifier votre niveau d’engagement, consultez [Définir ou modifier le niveau tarifaire](#set-or-change-pricing-tier).

### <a name="costs-for-other-services"></a>Coûts des autres services

Azure Sentinel s’intègre à de nombreux autres services Azure pour offrir des capacités améliorées. Ces services incluent Azure Logic Apps, Azure Notebooks et les modèles BYOML (Bring Your Own Machine Learning). Certains de ces services peuvent entraîner des frais supplémentaires. Certains connecteurs de données et solutions d’Azure Sentinel utilisent Azure Functions pour l’ingestion des données, qui a également un coût associé distinct.

Pour plus d’informations sur la tarification de ces services, consultez :

- [Tarification Automation-Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Tarification Notebooks](https://azure.microsoft.com/pricing/details/machine-learning/)
- [Tarification BYOML](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Tarification d’Azure Functions](https://azure.microsoft.com/en-us/pricing/details/functions/)

Tout autre service que vous utilisez peut avoir des coûts associés.

### <a name="data-retention-costs"></a>Coûts de conservation des données

Une fois que vous avez activé Azure Sentinel sur un espace de travail Log Analytics, vous pouvez conserver gratuitement toutes les données ingérées dans l’espace de travail pendant les 90 premiers jours. La conservation des données au-delà de 90 jours est facturée selon les [tarifs standard de Log Analytics pour la conservation des données](https://azure.microsoft.com/pricing/details/monitor/).

Vous pouvez spécifier différents paramètres de conservation des données pour des types de données individuels. Pour plus d’informations, consultez [Durée de conservation par type de données](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type).

### <a name="free-trial"></a>Essai gratuit

Vous pouvez activer Azure Sentinel sur un espace de travail Log Analytics nouveau ou existant sans frais supplémentaires pendant les 31 premiers jours. Les frais liés à Log Analytics, Automation et BYOML s’appliquent toujours pendant l’essai gratuit. L’utilisation au-delà des 31 premiers jours est facturée selon la [tarification d’Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel). 

### <a name="free-data-sources"></a>Sources de données gratuites

Les sources de données suivantes sont gratuites avec Azure Sentinel :

- Journaux d’activité Azure.
- Journaux d’audit d’Office 365, y compris toute l’activité SharePoint, l’activité de l’administrateur Exchange et Teams.
- Alertes de Microsoft Defender, y compris les alertes d’Azure Defender, de Microsoft 365 Defender, Microsoft Defender pour Office 365, Microsoft Defender pour Identity et Microsoft Defender pour point de terminaison.
- Alertes d’Azure Security Center et de Microsoft Cloud App Security (MCAS). Toutefois, les journaux bruts pour certains types de données de Microsoft 365 Defender, de MCAS, d’Azure Active Directory (Azure AD) et d’Azure Information Protection (AIP) sont payants.

Le tableau suivant répertorie les sources de données gratuites que vous pouvez activer dans Azure Sentinel. Certains connecteurs de données, tels que Microsoft 365 Defender et MCAS, incluent des types de données gratuits et payants.

| Connecteur de données d’Azure Sentinel   | Type de données | Gratuit ou payant |
|-------------------------------------|--------------------------------|------------------|
| **Journaux d’activité Azure**         | AzureActivity                  | Gratuit             |
| **Office 365**                     | OfficeActivity (SharePoint)    | Gratuit|
|| OfficeActivity (Exchange)|Gratuit|
|| OfficeActivity (Teams)          | Gratuit|
| **Azure Defender**                  | SecurityAlert (ASC)             | Gratuit             |
| **Azure Defender pour IoT**          | SecurityAlert (ASC pour IoT)     | Gratuit             |
| **Microsoft 365 Defender**          | SecurityIncident | Gratuit|
||SecurityAlert| Gratuit|
||DeviceEvents                    | Payant|
||DeviceFileEvents                | Payant|
||DeviceImageLoadEvents           | Payant|
||DeviceInfo                      | Payant|
||DeviceLogonEvents               | Payant|
||DeviceNetworkInfo             | Payant|
||DeviceNetworkInfo               | Payant|
||DeviceProcessEvents             | Payant|
||DeviceRegistryEvents            | Payant|
||DeviceFileCertificateInfo       | Payant|
| **Microsoft Defender for Endpoint** | SecurityAlert (MDATP)          | Gratuit             |
| **Microsoft Defender pour Identity** | SecurityAlert (AATP)           | Gratuit             |
| **Microsoft Cloud App Security**   | SecurityAlert (MCAS)           | Gratuit             |
||MCASShadowITReporting           | Payant|

Pour les connecteurs de données qui incluent à la fois des types de données gratuits et payants, vous pouvez sélectionner les types de données que vous souhaitez activer.

![Capture d’écran montrant la page Connecteurs de données pour MCAS, avec le type gratuit SecurityAlert sélectionné et le type payant MCASShadowITReporting non sélectionné.](media/billing/data-types.png)

## <a name="estimate-azure-sentinel-costs"></a>Estimer les coûts d’Azure Sentinel

Si vous n’utilisez pas encore Azure Sentinel, vous pouvez utiliser la [calculatrice de prix d’Azure Sentinel](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) pour estimer le coût potentiel de son utilisation. Entrez *Azure Sentinel* dans la zone de recherche, puis sélectionnez la vignette Azure Sentinel qui en résulte. La calculatrice de prix vous aide à estimer vos coûts probables en fonction de l’ingestion et de la conservation des données que vous prévoyez.

Par exemple, vous pouvez saisir le nombre de Go de données quotidiennes que vous prévoyez d’ingérer dans Azure Sentinel, ainsi que la région de votre espace de travail. La calculatrice fournit le coût mensuel global pour les composants suivants :

- Ingestion des données Log Analytics 
- Analyse des données Azure Sentinel 
- Conservation des données Log Analytics 

## <a name="manage-azure-sentinel-costs"></a>Gérer les coûts d’Azure Sentinel

Il existe plusieurs façons de comprendre et de gérer l’utilisation et les coûts d’Azure Sentinel.

Gérer l’ingestion et la conservation des données :

- [Utilisez la tarification par niveau d’engagement pour optimiser les coûts](#set-or-change-pricing-tier) en fonction de votre volume de données ingérées.
- [Définissez un plafond de volume de données Log Analytics](#define-a-data-volume-cap-in-log-analytics) pour gérer l’ingestion, bien que les données de sécurité soient exclues de ce plafond.
- [Optimisez les coûts de Log Analytics à l’aide de clusters dédiés](#optimize-log-analytics-costs-with-dedicated-clusters).
- [Séparez les données non relatives à la sécurité dans un espace de travail différent](#separate-non-security-data-in-a-different-workspace).
- [Réduisez les coûts de conservation des données à long terme grâce à Azure Data Explorer (ADX)](#reduce-long-term-data-retention-costs-with-adx).

Comprendre et surveiller les changements dans l’ingestion des données et les coûts et émettre des alertes à ce sujet :

- [Exécutez des requêtes pour comprendre votre ingestion des données](#run-queries-to-understand-your-data-ingestion).
- [Déployez un classeur pour visualiser l’ingestion des données](#deploy-a-workbook-to-visualize-data-ingestion).
- [Utilisez un guide opérationnel de gestion des coûts](#use-a-playbook-for-cost-management-alerts) qui peut envoyer une alerte lorsque l’ingestion dépasse un seuil prédéfini.
- [Comprenez l’ingestion des données au format CEF (Common Event Format)](#understand-cef-ingestion-volume).

### <a name="manage-data-ingestion-and-retention"></a>Gérer l’ingestion et la conservation des données

Utilisez les méthodes suivantes pour gérer l’ingestion et la conservation des données de votre espace de travail Azure Sentinel.

#### <a name="set-or-change-pricing-tier"></a>Définir ou modifier le niveau tarifaire

Pour optimiser les économies les plus importantes, surveillez votre volume d’ingestion pour vous assurer que vous disposez du niveau d’engagement qui correspond le mieux à vos modèles de volume d’ingestion. Vous pouvez augmenter ou diminuer votre niveau d’engagement pour vous aligner sur l’évolution des volumes de données.

Vous pouvez augmenter votre niveau d’engagement à tout moment, ce qui redémarre la période d’engagement de 31 jours. Toutefois, pour revenir à Paiement à l’utilisation ou à un niveau d’engagement inférieur, vous devez attendre la fin de la période d’engagement de 31 jours. La facturation des niveaux d’engagement se fait sur une base quotidienne.

Pour voir votre niveau tarifaire Azure Sentinel actuel, sélectionnez **Paramètres** dans le volet de navigation gauche d’Azure Sentinel, puis sélectionnez l’onglet **Tarification**. Votre niveau tarifaire actuel est marqué **Niveau actuel**.

Pour modifier votre engagement de niveau tarifaire, sélectionnez l’un des autres niveaux sur la page de tarification, puis sélectionnez **Appliquer**. Vous devez disposer du rôle **Contributeur** ou **Propriétaire** dans Azure Sentinel pour modifier le niveau tarifaire.

![Capture d’écran montrant la page Tarification dans les paramètres d’Azure Sentinel, avec Paiement à l’utilisation indiqué comme niveau tarifaire actuel.](media/billing/pricing.png)

> [!NOTE]
> Les volumes d’ingestion de données Azure Sentinel apparaissent sous **Insights sur la sécurité** dans certains graphiques d’utilisation du portail.

Les niveaux tarifaires d’Azure Sentinel n’incluent pas les frais de Log Analytics. Pour modifier votre engagement de niveau tarifaire pour Log Analytics, consultez [Changement de niveau tarifaire](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier).

#### <a name="define-a-data-volume-cap-in-log-analytics"></a>Définir un plafond de volume de données dans Log Analytics

Dans Log Analytics, vous pouvez activer un plafond de volume quotidien qui limite l’ingestion quotidienne de votre espace de travail. Le plafond quotidien peut vous aider à gérer les augmentations inattendues du volume de données, à rester sous votre limite et à limiter les frais non planifiés.

Pour définir un plafond de volume quotidien, sélectionnez **Utilisation et estimation des coûts** dans le volet de navigation gauche de votre espace de travail Log Analytics, puis sélectionnez **Plafond quotidien**. Sélectionnez **Activé**, entrez une valeur limite de volume quotidien, puis sélectionnez **OK**.

![Capture d’écran montrant l’écran Utilisation et estimation des coûts et la fenêtre Plafond quotidien.](media/billing/daily-cap.png)

L’écran **Utilisation et estimation des coûts** indique également la tendance du volume de données ingérées au cours des 31 derniers jours, ainsi que le volume total de données conservées.

> [!IMPORTANT]
> Le plafond quotidien ne limite pas la collecte de tous les types de données. Pour plus d’informations sur la gestion du plafond quotidien dans Log Analytics, consultez [Gérer votre volume de données maximal quotidien](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume).

#### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>Optimiser les coûts de Log Analytics à l’aide de clusters dédiés

Si vous ingérez au moins 1 To/jour dans votre espace de travail Azure Sentinel ou dans des espaces de travail de la même région, envisagez de passer à un cluster dédié Log Analytics pour réduire les coûts. Un niveau d’engagement pour cluster dédié Log Analytics agrège les volumes de données des espaces de travail qui ingèrent collectivement un total de 1 To/jour ou plus.

Les clusters dédiés Log Analytics ne s’appliquent pas aux niveaux d’engagement d’Azure Sentinel. Les coûts d’Azure Sentinel s’appliquent toujours par espace de travail dans le cluster dédié.

Vous pouvez ajouter plusieurs espaces de travail Azure Sentinel à un cluster dédié Log Analytics. L’utilisation d’un cluster dédié Log Analytics pour Azure Sentinel présente deux avantages :

- Les requêtes interespaces de travail s’exécutent plus rapidement si tous les espaces de travail impliqués dans la requête se trouvent dans le cluster dédié. Il est toujours préférable d’avoir le moins d’espaces de travail possible dans votre environnement, et un cluster dédié conserve toujours la [limite de 100 espaces de travail](../azure-monitor/logs/cross-workspace-query.md) à inclure dans une seule requête interespace de travail.

- Tous les espaces de travail du cluster dédié peuvent partager le niveau d’engagement Log Analytics défini sur le cluster. Le fait de ne pas avoir à s’engager sur des niveaux d’engagement Log Analytics distincts pour chaque espace de travail peut permettre de réaliser des économies et des gains d’efficacité. En activant un cluster dédié, vous vous engagez à un niveau d’engagement Log Analytics minimum de 1 To d’ingestion par jour.

Voici d’autres considérations à prendre en compte pour passer à un cluster dédié afin d’optimiser les coûts :

- Le nombre maximum de clusters par région et par abonnement est de deux.
- Tous les espaces de travail liés à un cluster doivent se trouver dans la même région.
- Le nombre maximal d’espaces de travail liés à un cluster est de 1000.
- Vous pouvez dissocier un espace de travail lié à votre cluster. Le nombre d’opérations de liaison sur un espace de travail particulier est limité à 2 par période de 30 jours.
- Vous ne pouvez pas déplacer un espace de travail existant vers un cluster avec clé gérée par le client (CMK). Vous devez créer l’espace de travail dans le cluster.
- Le déplacement d’un cluster vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge actuellement.
- Un lien d’espace de travail vers un cluster échoue si l’espace de travail est lié à un autre cluster.

Pour plus d’informations sur les clusters dédiés, consultez [Clusters dédiés Log Analytics](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

#### <a name="separate-non-security-data-in-a-different-workspace"></a>Séparer les données non relatives à la sécurité dans un espace de travail différent

Azure Sentinel analyse toutes les données ingérées dans les espaces de travail Log Analytics avec Azure Sentinel. Il est préférable de disposer d’un espace de travail distinct pour les données d’opérations non liées à la sécurité, afin de s’assurer qu’elles n’entraînent pas de coûts d’Azure Sentinel.

Lors de la chasse ou de l’investigation des menaces dans Azure Sentinel, vous pouvez avoir besoin d’accéder aux données opérationnelles stockées dans ces espaces de travail Azure Log Analytics autonomes. Vous pouvez accéder à ces données en utilisant des requêtes interespaces de travail dans l’expérience d’exploration des journaux et les classeurs. Toutefois, vous ne pouvez pas utiliser les règles d’analyse et les requêtes de chasse interespaces de travail, sauf si Azure Sentinel est activé sur tous les espaces de travail.

#### <a name="reduce-long-term-data-retention-costs-with-adx"></a>Réduire les coûts de conservation des données à long terme grâce à ADX

La conservation des données Azure Sentinel est gratuite pendant les 90 premiers jours. Pour ajuster la période de conservation des données dans Log Analytics, sélectionnez **Utilisation et estimation des coûts** dans le volet de navigation gauche, puis sélectionnez **Conservation des données** et réglez le curseur.

Les données de sécurité d’Azure Sentinel peuvent perdre une partie de leur valeur après quelques mois. Les utilisateurs du centre des opérations de sécurité (SOC) n’ont peut-être pas besoin d’accéder aux anciennes données aussi fréquemment qu’aux nouvelles, mais ils peuvent tout de même avoir besoin d’accéder aux données à des fins d’investigation ou d’audit sporadiques. Pour réduire les coûts de conservation des données d’Azure Sentinel, vous pouvez utiliser Azure Data Explorer pour la conservation des données à long terme à moindre coût. ADX offre un juste équilibre entre coût et convivialité pour les données vieillies qui n’ont plus besoin des renseignements sur la sécurité d’Azure Sentinel.

Grâce à ADX, vous pouvez stocker des données à moindre coût, tout en continuant à les explorer à l’aide des mêmes requêtes KQL (Kusto Query Language) que dans Azure Sentinel. Vous pouvez également utiliser la fonctionnalité de proxy d’ADX pour effectuer des requêtes multiplateformes. Ces requêtes agrègent et mettent en corrélation les données réparties dans ADX, Application Insights, Azure Sentinel et Log Analytics.

Pour plus d’informations, consultez [Intégrer Azure Data Explorer pour la conservation des journaux à long terme](store-logs-in-azure-data-explorer.md).

### <a name="understand-monitor-and-alert-for-changes-in-data-ingestion-and-costs"></a>Comprendre et surveiller les changements dans l’ingestion des données et les coûts et émettre des alertes à ce sujet

Utilisez les méthodes suivantes pour comprendre et surveiller les changements dans votre espace de travail Azure Sentinel et émettre des alertes à ce sujet.

#### <a name="run-queries-to-understand-your-data-ingestion"></a>Exécuter des requêtes pour comprendre votre ingestion des données

Voici quelques-unes des requêtes que vous pouvez utiliser pour comprendre votre volume d’ingestion de données.

Exécutez la requête suivante pour afficher le volume d’ingestion de données par solution : 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

Exécutez la requête suivante pour afficher le volume d’ingestion de données par type de données : 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```
Exécutez la requête suivante pour afficher le volume d’ingestion de données par solution et par type de données : 

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

#### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>Déployer un classeur pour visualiser l’ingestion des données

Le classeur **Rapport sur l’utilisation de l’espace de travail** fournit des statistiques sur la consommation, le coût et l’utilisation des données de votre espace de travail. Le classeur indique l’état d’ingestion des données de l’espace de travail et la quantité de données gratuites et facturables. Vous pouvez utiliser la logique du classeur pour surveiller l’ingestion de données et les coûts et pour créer des vues personnalisées et des alertes basées sur des règles.

Ce classeur fournit également des détails granulaires sur l’ingestion. Le classeur décompose les données de votre espace de travail par table de données et fournit des volumes par table et par entrée pour vous aider à mieux comprendre vos modèles d’ingestion.

Pour activer le classeur Rapport sur l’utilisation de l’espace de travail :

1. Dans le volet de navigation gauche d’Azure Sentinel, sélectionnez **Gestion des menaces** > **Classeurs**.
1. Entrez *utilisation de l’espace de travail* dans la barre de recherche, puis sélectionnez **Rapport sur l’utilisation de l’espace de travail**.
1. Sélectionnez **Afficher le modèle** pour utiliser le classeur tel quel, ou **Enregistrer** pour créer une copie modifiable du classeur. Si vous enregistrez une copie, sélectionnez **Afficher le classeur enregistré**.
1. Dans le classeur, sélectionnez l’**abonnement** et l’**espace de travail** que vous souhaitez afficher, puis définissez le **TimeRange** (intervalle de temps) sur la période que vous souhaitez visualiser. Vous pouvez régler le bouton bascule **Afficher l’aide** sur **Oui** pour afficher des explications intégrées dans le classeur.

#### <a name="use-a-playbook-for-cost-management-alerts"></a>Utiliser un guide opérationnel pour les alertes relatives à la gestion des coûts

Pour vous aider à contrôler votre budget Azure Sentinel, vous pouvez créer un guide opérationnel de gestion des coûts. Le guide opérationnel vous envoie une alerte si votre espace de travail Azure Sentinel dépasse un budget, que vous définissez, dans un délai donné.

La communauté GitHub d’Azure Sentinel fournit le guide opérationnel de gestion des coûts [Send-IngestionCostAlert](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) sur le site de GitHub. Ce guide opérationnel est activé par un déclencheur de périodicité et vous offre un niveau élevé de flexibilité. Vous pouvez contrôler la fréquence d’exécution, le volume d’ingestion et le message à déclencher, en fonction de vos besoins.

#### <a name="understand-cef-ingestion-volume"></a>Comprendre le volume d’ingestion CEF

Le CEF est un format d’événements Syslog pris en charge dans Azure Sentinel. Vous pouvez utiliser le CEF pour apporter de précieuses informations de sécurité provenant de diverses sources à votre espace de travail Azure Sentinel. Les journaux CEF atterrissent dans la table CommonSecurityLog d’Azure Sentinel, qui comprend tous les champs CEF standard à jour.

De nombreux appareils et sources de données autorisent des champs de journalisation au-delà du schéma CEF standard. Ces champs supplémentaires atterrissent dans la table AdditionalExtensions. Ces champs peuvent avoir des volumes d’ingestion plus élevés que les champs CEF standard, car le contenu des événements dans ces champs peut être variable.

## <a name="understand-your-azure-sentinel-costs-and-bill"></a>Comprendre vos coûts et votre facture Azure Sentinel

Il est important de comprendre et de suivre vos coûts Azure Sentinel. Le hub [Azure Cost Management and Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) offre une fonctionnalité utile. Après avoir ouvert **Cost Management and Billing** dans le portail Azure, sélectionnez **Cost Management** dans le volet de navigation gauche, puis sélectionnez l’[étendue](..//cost-management-billing/costs/understand-work-scopes.md) ou l’ensemble des ressources à examiner, par exemple un abonnement Azure ou un groupe de ressources.

Pour voir votre facture Azure, sélectionnez **Analyse des coûts** dans le volet de navigation gauche de **Cost Management and Billing**. Sur l’écran **Analyse des coûts**, sélectionnez le caret déroulant dans le champ **Afficher**, puis sélectionnez **Détails de la facture**.

> [!NOTE]
> Les coûts indiqués dans cette image sont donnés à titre d’exemple uniquement. Ils ne sont pas destinés à refléter les coûts réels.

![Capture d’écran montrant la section Azure Sentinel d’un exemple de facture Azure.](media/billing/sample-bill.png)

Les frais relatifs à Azure Sentinel et à Log Analytics s’affichent sur votre facture Azure sous la forme de lignes distinctes en fonction du plan tarifaire sélectionné. Si vous dépassez l’utilisation du niveau d’engagement de votre espace de travail au cours d’un mois donné, la facture Azure présente une ligne pour le niveau d’engagement avec son coût fixe associé, et une ligne distincte pour l’ingestion au-delà du niveau d’engagement, facturée au même tarif du niveau d’engagement.

Le tableau suivant montre la manière dont les coûts Azure Sentinel et Log Analytics apparaissent dans les colonnes **Nom du service** et **Compteur** de votre facture Azure :

|Coût|Nom du service|Compteur|
|----|------------|----------------|
|Niveau d’engagement Azure Sentinel|**sentinel**|**niveau d’engagement de `n` Go**|
|Niveau d’engagement Log Analytics|**azure monitor**|**niveau d’engagement de `n` Go**|
|Dépassement d’Azure Sentinel par rapport au niveau d’engagement ou à Paiement à l’utilisation|**sentinel**|**analyse**|
|Dépassement de Log Analytics par rapport au niveau d’engagement ou à Paiement à l’utilisation|**log analytics**|**ingestion des données**|

Pour plus d’informations sur l’affichage et le téléchargement de votre facture Azure, consultez les [informations relatives aux coûts et à la facturation d’Azure](../cost-management-billing/understand/download-azure-daily-usage.md).

L’écran **Analyse des coûts** présente également des vues détaillées de votre utilisation et de vos coûts liés à Azure, avec la possibilité d’appliquer divers contrôles et filtres.

Par exemple, pour voir les graphiques de vos coûts quotidiens pour une certaine période :
1. Cliquez sur le caret déroulant dans le champ **Afficher** et sélectionnez **Coûts cumulés** ou **Coûts quotidiens**.
1. Cliquez sur le caret déroulant dans le champ Date et sélectionnez une plage de dates.
1. Cliquez sur le caret déroulant à côté de **Granularité** et sélectionnez **Quotidien**.

> [!NOTE]
> Les coûts indiqués dans cette image sont donnés à titre d’exemple uniquement. Ils ne sont pas destinés à refléter les coûts réels.

![Capture d’écran montrant un écran Analyse des coûts de Cost Management and Billing.](media/billing/cost-management.png)

Vous pouvez également appliquer d’autres contrôles. Par exemple, pour afficher uniquement les coûts associés à Azure Sentinel, sélectionnez **Ajouter un filtre**, **Nom du service**, puis les noms de service **sentinel**, **log analytics** et **azure monitor**.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires sur la réduction du volume de données Log Analytics, consultez [Conseils pour réduire le volume de données](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume).