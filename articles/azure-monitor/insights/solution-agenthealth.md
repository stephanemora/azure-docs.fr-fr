---
title: Solution agent Health Azure Monitor | Microsoft Docs
description: Cet article a pour but de vous apprendre à utiliser cette solution afin de surveiller l’intégrité de vos agents au service de Log Analytics ou de System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: f431613d9fa1020f523e03c90cbe31f4d42ccf42
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596067"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Solution agent Health Azure Monitor
La solution Agent Health dans Azure vous permet de comprendre, pour tous les agents de rapports directement à l’espace de travail Analytique de journal dans Azure Monitor ou un groupe d’administration System Center Operations Manager connecté à Azure Monitor, qui sont ne répond pas et envoi de données opérationnelles.  Vous pouvez aussi suivre le nombre d’agents déployés et leur localisation géographique, et réaliser diverses requêtes pour être au fait de la distribution d’agents déployés au sein d’Azure, d’environnements de cloud ou localement.    

## <a name="prerequisites"></a>Conditions préalables
Avant de déployer cette solution, veuillez confirmer que vous avez bien pris en charge des [agents Windows](../../log-analytics/log-analytics-windows-agent.md) au service de l’espace de travail Log Analytics ou d’un [groupe d’administration Operations Manager](../../azure-monitor/platform/om-agents.md) inclut dans votre espace de travail.

## <a name="solution-components"></a>Composants de la solution
Cette solution se compose des ressources suivantes qui sont ajoutées à votre espace de travail et d’agents directement connectés ou d’un groupe d’administration Operations Manager connecté.

### <a name="management-packs"></a>Packs d’administration
Si votre groupe d’administration System Center Operations Manager est connecté à un espace de travail Log Analytics, les packs d’administration suivants sont installés dans Operations Manager.  Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de cette solution. Aucun élément ne doit être configuré ou géré avec ces packs d’administration.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configuration
Ajoutez la solution Agent Health à votre espace de travail Log Analytics en suivant la procédure décrite dans [Ajouter des solutions](solutions.md). Aucune configuration supplémentaire n’est requise.


## <a name="data-collection"></a>Collecte des données
### <a name="supported-agents"></a>Agents pris en charge
Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Pris en charge | Description |
| --- | --- | --- |
| Agents Windows | Oui | Les événements de pulsation sont rassemblés par des agents Windows directs.|
| Groupe d’administration Microsoft System Center Operations Manager | Oui | Événements de pulsation sont collectées à partir des agents qui communiquent avec le groupe d’administration toutes les 60 secondes et ensuite transmis à Azure Monitor. Une connexion directe entre les agents Operations Manager et Azure Monitor n’est pas requise. Données d’événement de pulsation sont transférées du groupe d’administration à l’espace de travail Analytique de journal.|

## <a name="using-the-solution"></a>Utilisation de la solution
Quand vous ajoutez la solution à votre espace de travail Log Analytics, la vignette **Agent Health** est ajoutée à votre tableau de bord. La vignette indique le nombre total d’agents et le nombre d’agents inactifs au cours des dernières 24 heures.<br><br> ![Vignette de la solution Agent Health du tableau de bord](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Cliquez sur la vignette **Agent Health** pour ouvrir le tableau de bord **Agent Health**.  Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne répertorie les dix premiers événements, classés selon leur nombre, correspondant aux critères de cette colonne pour l’intervalle de temps spécifié. Vous pouvez exécuter une recherche dans les journaux qui fournit la liste complète. Pour cela, sélectionnez **Afficher tout** dans l’angle inférieur droit de chaque colonne ou cliquez sur l’en-tête de colonne.

| Colonne | Description |
|--------|-------------|
| Nombre d’agents au fil du temps | Tendance du nombre d’agents sur une période de sept jours (agents Linux et Windows).|
| Nombre d’agents inactifs | Liste des agents n’ayant émis aucune pulsation au cours des dernières 24 heures.|
| Distribution selon le système d’exploitation | Partition du nombre d’agents Windows et Linux dont vous disposez dans votre environnement.|
| Distribution selon la version de l’agent | Partition des versions des agents installées dans votre environnement, et leur nombre.|
| Distribution selon la catégorie de l’agent | Partition des différentes catégories d’agents émettant des événements de pulsation : agents directs, agents Operations Manager ou serveur d’administration Operations Manager.|
| Distribution selon le groupe d’administration | Une partition des différents groupes d’administration Operations Manager dans votre environnement.|
| Géolocalisation des agents | Partition des différents pays dans lesquels vous disposez d’agents et nombre total d’agents installés par pays.|
| Nombre de passerelles installées | Nombre et liste des serveurs disposant de la passerelle Log Analytics.|

![Exemple de tableau de bord pour la solution Agent Health](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor
La solution crée un type d’enregistrement dans l’espace de travail Log Analytics.  

### <a name="heartbeat-records"></a>Enregistrements de pulsation
L’enregistrement d’un type de **pulsation** est créé.  Les propriétés de ces enregistrements sont décrites dans le tableau suivant.  

| Propriété | Description |
| --- | --- |
| `Type` | *Pulsation*|
| `Category` | La valeur correspond à *Agent Direct*, *Agent SCOM*, ou *Serveur d’administration SCOM*.|
| `Computer` | Nom de l’ordinateur.|
| `OSType` | Système d’exploitation : Windows ou Linux.|
| `OSMajorVersion` | Version principale du système d’exploitation.|
| `OSMinorVersion` | Version secondaire du système d’exploitation.|
| `Version` | Version de l’agent Log Analytics ou de l’agent Operations Manager.|
| `SCAgentChannel` | La valeur correspond à *Direct* et/ou *SCManagementServer*.|
| `IsGatewayInstalled` | Si la passerelle Log Analytics est installée, la valeur correspond à *true*. Dans le cas contraire, elle correspond à *false*.|
| `ComputerIP` | Adresse IP de l’ordinateur.|
| `RemoteIPCountry` | Lieu où l’ordinateur est déployé.|
| `ManagementGroupName` | Nom du groupe d’administration Operations Manager.|
| `SourceComputerId` | ID unique de l’ordinateur.|
| `RemoteIPLongitude` | Longitude de l’emplacement géographique de l’ordinateur.|
| `RemoteIPLatitude` | Latitude de l’emplacement géographique de l’ordinateur.|

Chaque agent rendant compte à un serveur d’administration Operations Manager envoie deux pulsations et la valeur de propriété SCAgentChannel inclura les deux **Direct** et **SCManagementServer** selon ce que sources de données et les solutions de surveillance que vous avez activé dans votre abonnement. Si vous vous rappelez, données à partir de solutions sont soit envoyées directement à partir d’un serveur d’administration Operations Manager à Azure Monitor, ou en raison du volume de données collectées sur l’agent, sont envoyées directement à partir de l’agent à Azure Monitor. Pour les événements de pulsation dont la valeur correspond à **SCManagementServer**, la valeur ComputerIP correspond à l’adresse IP du serveur d’administration, car les données sont chargées par ce dernier.  Pour les pulsations dont la valeur SCAgentChannel correspond à **Direct**, la valeur ComputerIP correspond à l’adresse IP publique de l’agent.  

## <a name="sample-log-searches"></a>Exemples de recherches de journaux
Le tableau suivant fournit des exemples de recherches de journaux pour les enregistrements collectés par cette solution.

| Requête | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Nombre total d’agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Nombre d’agents inactifs au cours des dernières 24 heures |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Nombre d’agents inactifs au cours des 15 dernières heures |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Ordinateurs en ligne (au cours des dernières 24 heures) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total des agents hors ligne au cours des 30 dernières minutes (au cours des dernières 24 heures) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Obtenir une tendance du nombre d’agents sur une période selon le système d’exploitation|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution selon le système d’exploitation |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution selon la version de l’agent |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution selon la catégorie de l’agent |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution selon le groupe d’administration |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Géolocalisation des agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Nombre de passerelles Log Analytics installées |




## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [alertes dans Azure Monitor](../platform/alerts-overview.md) pour plus d’informations sur la génération d’alertes à partir de requêtes de journal. 
