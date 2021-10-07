---
title: Solution Agent Health dans Azure Monitor | Microsoft Docs
description: Découvrez comment utiliser cette solution afin de superviser l’intégrité de vos agents qui rendent directement compte à Log Analytics ou System Center Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663437"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Solution Agent Health dans Azure Monitor
La solution Agent Health dans Azure vous aide à comprendre quels agents de supervision ne répondent pas et envoient des données opérationnelles. Cela comprend tous les agents qui rendent directement compte à l’espace de travail Log Analytics dans Azure Monitor ou à un groupe d’administration System Center Operations Manager connecté à Azure Monitor.

Vous pouvez également utiliser la solution Agent Health pour :

* Effectuer le suivi du nombre d’agents déployés et de leur distribution géographique.
* Effectuer d’autres requêtes pour être tenus à jour de la distribution des agents déployés dans Azure, dans d’autres environnements cloud ou localement.    

## <a name="prerequisites"></a>Prérequis
Avant de déployer cette solution, confirmez que vous avez pris en charge des [agents Windows](../agents/agent-windows.md) qui rendent compte à l’espace de travail Log Analytics ou à un [groupe d’administration Operations Manager](../agents/om-agents.md) intégré à votre espace de travail.

## <a name="management-packs"></a>Packs d’administration
Si votre groupe d’administration Operations Manager est connecté à un espace de travail Log Analytics, les packs d’administration suivants sont installés dans Operations Manager. Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de cette solution.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Aucun élément ne doit être configuré ou géré avec ces packs d’administration. Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](../agents/om-agents.md).

## <a name="configuration"></a>Configuration
Ajoutez la solution Agent Health à votre espace de travail Log Analytics en suivant la procédure décrite dans [Ajouter des solutions](solutions.md). Aucune configuration supplémentaire n'est nécessaire.

## <a name="supported-agents"></a>Agents pris en charge
Le tableau suivant décrit les sources connectées prises en charge par cette solution.

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows | Oui | Les événements de pulsation sont rassemblés par des agents Windows directs.|
| Groupe d’administration Microsoft System Center Operations Manager | Oui | Les événements de pulsation sont collectés toutes les 60 secondes par les agents qui rendent compte au groupe d’administration, et sont ensuite transférés à Azure Monitor. Une connexion directe entre les agents Operations Manager et Azure Monitor n’est pas obligatoire. Les données d’événement de pulsation sont transférées depuis le groupe d’administration vers l’espace de travail Log Analytics.|

## <a name="using-the-solution"></a>Utilisation de la solution
Quand vous ajoutez la solution à votre espace de travail Log Analytics, la vignette **Agent Health** est ajoutée à votre tableau de bord. La vignette indique le nombre total d’agents et le nombre d’agents inactifs au cours des dernières 24 heures.

![Capture d’écran montrant la vignette Agent Health sur le tableau de bord.](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Sélectionnez la vignette **Agent Health** pour ouvrir le tableau de bord **Agent Health**.  Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne liste les dix premiers événements, classés selon leur nombre, correspondant aux critères de cette colonne pour l’intervalle de temps spécifié. Vous pouvez exécuter une recherche dans les journaux qui fournit la liste complète en sélectionnant **Afficher tout** sous chaque colonne, ou en sélectionnant l’en-tête de colonne.

| Colonne | Description |
|--------|-------------|
| Nombre d’agents au fil du temps | Tendance du nombre d’agents sur une période de sept jours (agents Linux et Windows)|
| Nombre d’agents inactifs | Liste des agents n’ayant émis aucune pulsation au cours des dernières 24 heures|
| Distribution selon le type de système d’exploitation | Partition du nombre d’agents Windows et Linux dont vous disposez dans votre environnement|
| Distribution selon la version de l’agent | Partition des versions des agents installées dans votre environnement, et leur nombre|
| Distribution selon la catégorie de l’agent | Partition des catégories d’agents émettant des événements de pulsation : agents directs, agents Operations Manager ou serveur d’administration Operations Manager|
| Distribution selon le groupe d’administration | Partition des groupes d’administration Operations Manager de votre environnement|
| Géolocalisation des agents | Partition des pays/régions dans lesquels vous disposez d’agents, et nombre total d’agents installés par pays/région|
| Nombre de passerelles installées | Nombre et liste des serveurs disposant de la passerelle Log Analytics|

![Capture d’écran montrant un exemple de tableau de bord de la solution Agent Health.](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor
La solution crée un type d’enregistrement dans l’espace de travail Log Analytics : pulsation. Les propriétés des enregistrements de pulsation sont décrites dans le tableau suivant :  

| Propriété | Description |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`, `SCOM Agent`ou `SCOM Management Server`|
| `Computer` | Nom de l'ordinateur|
| `OSType` | Système d’exploitation : Windows ou Linux|
| `OSMajorVersion` | Version principale du système d’exploitation|
| `OSMinorVersion` | Version secondaire du système d’exploitation|
| `Version` | Version de l’agent Log Analytics ou de l’agent Operations Manager|
| `SCAgentChannel` | `Direct` et/ou `SCManagementServer`|
| `IsGatewayInstalled` | `true` si la passerelle Log Analytics est installée ; sinon, `false`|
| `ComputerIP` | Adresse IP publique d’une machine virtuelle Azure, si celle-ci est disponible ; Adresse SNAT Azure (et non l’adresse IP privée) d’une machine virtuelle qui utilise une adresse IP privée |
| `ComputerPrivateIPs` | Liste des adresses IP privées de l’ordinateur |
| `RemoteIPCountry` | Lieu où l’ordinateur est déployé|
| `ManagementGroupName` | Nom du groupe d’administration Operations Manager|
| `SourceComputerId` | ID unique de l’ordinateur|
| `RemoteIPLongitude` | Longitude de l’emplacement géographique de l’ordinateur|
| `RemoteIPLatitude` | Latitude de l’emplacement géographique de l’ordinateur|

Chaque agent qui rend compte à un serveur d’administration Operations Manager envoie deux pulsations. La valeur de la propriété `SCAgentChannel` inclura à la fois `Direct` et `SCManagementServer`, en fonction des sources de données et des solutions de supervision que vous avez activées dans votre abonnement. 

Souvenez-vous que les données des solutions sont envoyées :

* Directement d’un serveur d’administration Operations Manager vers Azure Monitor, ou
* Directement de l’agent vers Azure Monitor, en raison du volume de données collectées sur l’agent.

Pour les événements de pulsation ayant la valeur `SCManagementServer`, la valeur `ComputerIP` est l’adresse IP du serveur d’administration, car c’est elle qui charge les données. Pour les pulsations dont la propriété `SCAgentChannel` a la valeur `Direct`, il s’agit de l’adresse IP publique de l’agent.  

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux
Le tableau suivant fournit des exemples de recherches dans les journaux pour les enregistrements collectés par cette solution.

| Requête | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Nombre total d’agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Nombre d’agents inactifs au cours des dernières 24 heures |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Nombre d’agents inactifs au cours des 15 dernières heures |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Ordinateurs en ligne au cours des dernières 24 heures |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total des agents hors ligne au cours des 30 dernières minutes (au cours des dernières 24 heures) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Tendance du nombre d’agents au fil du temps par système d’exploitation|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution selon le type de système d’exploitation |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution selon la version de l’agent |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution selon la catégorie de l’agent |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution selon le groupe d’administration |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Géolocalisation des agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Nombre de passerelles Log Analytics installées |

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur la [génération d’alertes à partir de requêtes de journal](../alerts/alerts-overview.md). 

