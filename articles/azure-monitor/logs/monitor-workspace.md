---
title: Surveiller l’intégrité d’un espace de travail Log Analytics dans Azure Monitor
description: Décrit comment surveiller l’intégrité de votre espace de travail Log Analytics à l’aide des données du tableau Opération.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 4f127245ea36a7183603f5115739d317282ea686
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113009521"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Surveiller l’intégrité d’un espace de travail Log Analytics dans Azure Monitor
Pour maintenir les performances et la disponibilité de votre espace de travail Log Analytics dans Azure Monitor, vous devez être en mesure de détecter de façon proactive les problèmes qui surviennent. Cet article décrit comment surveiller l’intégrité de votre espace de travail Log Analytics à l’aide des données du tableau [Opération](/azure/azure-monitor/reference/tables/operation). Ce tableau, qui figure dans tous les espaces de travail Log Analytics, présente les erreurs et les avertissements qui surviennent dans votre espace de travail. Il est recommandé de créer des alertes pour les problèmes de niveau « Avertissement » ou « Erreur ».

## <a name="_logoperation-function"></a>Fonction _LogOperation

Azure Monitor Logs envoie des informations relatives aux problèmes au tableau [Opération](/azure/azure-monitor/reference/tables/operation) de l’espace de travail concerné. La fonction système **_LogOperation** est basée sur le tableau **Opération** et fournit un jeu d’informations simplifié pour l’analyse et la création d’alertes.

## <a name="columns"></a>Colonnes

La fonction **_LogOperation** renvoie les colonnes du tableau suivant.

| Colonne | Description |
|:---|:---|
| TimeGenerated | Heure (UTC) à laquelle l’incident est survenu. |
| Category  | Groupe de catégorie de l’opération. Peut être utilisé pour filtrer sur les types d’opérations et créer des audits et des alertes système plus précis. La liste des catégories figure dans la section ci-dessous. |
| Opération  | Description du type d’opération. L’opération peut indiquer qu’une des limites de Log Analytics a été atteinte, un problème lié au processus principal ou tout autre message de service. |
| Level | Niveau de gravité du problème :<br>- Info : Aucune attention particulière n’est requise.<br>- Avertissement : Le processus ne s’est pas terminé comme prévu, votre attention est requise.<br>- Erreur : Un processus a échoué et nécessite votre attention. 
| Détail | Description détaillée de l’opération, inclut le message d’erreur spécifique. |
| _ResourceId | ID de ressource de la ressource Azure liée à l’opération.  |
| Computer | Nom de l’ordinateur si l’opération est liée à un agent Azure Monitor. |
| CorrelationId | Utilisé pour regrouper les opérations liées consécutives. |


## <a name="categories"></a>Catégories

Le tableau suivant décrit les catégories de la fonction _LogOperation. 

| Category | Description |
|:---|:---|
| Ingestion           | Opérations faisant partie du processus d’ingestion de données. |
| Agent               | Indique un problème lié à l’installation de l’agent. |
| Collecte de données     | Opérations liées aux processus de collecte des données. |
| Ciblage de la solution  | L’opération de type *ConfigurationScope* a été traitée. |
| Solution d’évaluation | Un processus d’évaluation a été exécuté. |


### <a name="ingestion"></a>Ingestion
Les opérations d’ingestion sont les problèmes qui sont survenus pendant l’ingestion de données, comme les notifications concernant les limites de l’espace de travail Azure Log Analytics atteintes. Les conditions d’erreur de cette catégorie peuvent indiquer une perte de données et il est intéressant de les surveiller. Le tableau suivant fournit des informations détaillées sur ces opérations. Pour connaître les limites de service des espaces de travail Log Analytics, consultez [Limites de services Azure Monitor](../service-limits.md#log-analytics-workspaces).

 
#### <a name="operation-data-collection-stopped"></a>Opération : Collecte de données arrêtée  
« Collecte de données arrêtée car la limite quotidienne de données gratuites a été atteinte. État de l’ingestion = Dépassement de quota »

Au cours des 7 derniers jours, la collecte des journaux a atteint la limite quotidienne définie. La limite est définie, car l’espace de travail est défini sur « niveau gratuit », ou la limite de collecte quotidienne a été configurée pour cet espace de travail.
Notez qu’après avoir atteint la limite définie, votre collecte de données s’arrêtera automatiquement pour la journée et reprendra uniquement au cours du jour de collecte suivant. 
 
Actions recommandées : 
*   Dans la table _LogOperation, recherchez les événements d’arrêt et de reprise de collecte.</br>
`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Data collection"`
*   [Créez une alerte](./manage-cost-storage.md#alert-when-daily-cap-is-reached) pour l’événement d’opération « La collecte de données s’est arrêtée ». Cette alerte vous permet de recevoir une notification une fois la limite de collecte atteinte.
*   Les données collectées une fois la limite quotidienne de collecte atteinte sont perdues. Utilisez le panneau « insights d’espace de travail » pour passer en revue les taux d’utilisation de chaque source. Ou, vous pouvez décider de ([gérer votre volume de données quotidien maximal](./manage-cost-storage.md#manage-your-maximum-daily-data-volume) \ )[changer le niveau de tarification](./manage-cost-storage.md#changing-pricing-tier) en un niveau qui conviendra à votre modèle de taux de collecte. 
* Le taux de collecte de données est calculé par jour et sera réinitialisé au début de la journée suivante, vous pouvez également surveiller l’événement de reprise de collecte en [créant une alerte](./manage-cost-storage.md#alert-when-daily-cap-is-reached) sur l’événement d’opération « Reprise de la collecte des données ».

#### <a name="operation-ingestion-rate"></a>Opération : Taux d’ingestion
« Le débit du volume d’ingestion de données a dépassé le seuil dans votre espace de travail : {0:0,00} Mo par minute et les données ont été supprimées. » 

Actions recommandées :
*   Dans la table _LogOperation, recherchez un événement de taux d’ingestion `_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Ingestion rate"` 
  Remarque : vérifiez la table des opérations dans l’espace de travail toutes les 6 heures, tandis que le seuil continue d’être dépassé. 
*   [Créez une alerte](./manage-cost-storage.md#alert-when-daily-cap-is-reached) pour l’événement d’opération « La collecte de données s’est arrêtée ». Cette alerte vous permet de recevoir une notification une fois la limite atteinte.
*   Les données collectées pendant que le taux d’ingestion a atteint 100 % seront abandonnées et perdues. 

Vérifiez le panneau « Insights d’espace de travail » pour passer en revue vos modèles d’utilisation et essayer de les réduire.</br>
Pour plus d’informations : </br>
[Limites du service Azure Monitor](../service-limits.md#data-ingestion-volume-rate) </br>
[Gérer l’utilisation et les coûts à l’aide des journaux d’activité Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-is-reached)  

 
#### <a name="operation-maximum-table-column-count"></a>Opération : Nombre maximal de colonnes de table
« Les données de type \<**table name**\> ont été supprimées, car le nombre de champs \<**new fields count**\> est supérieur à la limite des \<**current field count limit**\> champs personnalisés par type de données ». 

Actions recommandées : Pour les tables personnalisées, vous pouvez passer à l’[analyse des données](./parse-text.md) dans les requêtes.

#### <a name="operation-field-content-validation"></a>Opération : Validation du contenu du champ
« Les valeurs \<**field name**\> des champs suivants de type \<**table name**\> ont été tronquées à la taille maximale autorisée, \<**field size limit**\> octets. Ajustez votre entrée en conséquence. » 

Un champ dépassant la taille limite a été traité par les journaux Azure, le champ a été réduit à la limite de champ autorisée. Nous vous déconseillons d’envoyer des champs dépassant la limite autorisée, car cela entraîne une perte de données. 

Actions recommandées : Vérifiez la source du type de données affecté :
*   Si les données sont envoyées via l’API du collecteur de données HTTP, vous devez modifier votre code\script pour fractionner les données avant qu’elles ne soient ingérées.
*   Pour les journaux personnalisés, collectés par l’agent Log Analytics, modifiez les paramètres de journalisation de l’application\de l’outil.
*   Pour tout autre type de données, déclenchez un cas de support. 
</br>Pour en savoir plus : [Limites du service Azure Monitor](../service-limits.md#data-ingestion-volume-rate) 

### <a name="data-collection"></a>Collecte de données
#### <a name="operation-azure-activity-log-collection"></a>Opération : Collecte des journaux d’activité Azure
« L’accès à l’abonnement a été perdu. Assurez-vous que l’abonnement \<**subscription id**\> est dans le locataire Azure Active Directory \<**tenant id**\>. Si l’abonnement est transféré vers un autre locataire, il n’y a aucun impact sur les services, mais les informations du locataire peuvent prendre jusqu’à une heure pour se propager. '"

Description : Dans certains cas, comme le déplacement d’un abonnement vers un autre locataire, les journaux d’activité Azure peuvent cesser de circuler dans l’espace de travail. Dans ces cas, nous devons reconnecter l’abonnement en suivant le processus décrit dans cet article.

Actions recommandées : 
* Si l’abonnement mentionné dans le message d’avertissement n’existe plus, accédez au panneau « Journal d’activité Azure » sous « Sources de données de l’espace de travail », sélectionnez l’abonnement approprié, puis sélectionnez le bouton « Déconnecter ».
* Si vous n’avez plus accès à l’abonnement mentionné dans le message d’avertissement :
  * Suivez l’étape 1 pour déconnecter l’abonnement. 
  * Pour continuer à collecter des journaux à partir de cet abonnement, contactez le propriétaire de l’abonnement pour corriger les autorisations et réactiver la collecte des journaux d’activité.
* [Créez un paramètre de diagnostic](../essentials/activity-log.md#send-to-log-analytics-workspace) pour envoyer le journal d’activité à un espace de travail Log Analytics. 

### <a name="agent"></a>Agent
#### <a name="operation-linux-agent"></a>Opération : Agent Linux
« Deux applications de configuration successives des paramètres OMS ont échoué »

Les paramètres de configuration sur le portail ont changé.

Action recommandée : Ce problème est généré en cas de problème de récupération des nouveaux paramètres de configuration par l’agent. Pour atténuer ce problème, vous devrez réinstaller l’agent. Dans la table_LogOperation, recherchez l’événement de l’agent.</br>

 `_LogOperation | where TimeGenerated >= ago(6h) | where Category == "Agent" | where Operation == "Linux Agent"  | distinct _ResourceId`

La liste répertorie les ID de ressource pour lesquels l’agent a une mauvaise configuration.
Pour atténuer ce problème, vous devrez réinstaller les agents répertoriés.

   

## <a name="alert-rules"></a>Règles d'alerte
Utilisez les [alertes de requête de journal](../alerts/alerts-log-query.md) dans Azure Monitor pour être informé en amont lorsqu’un problème est détecté dans votre espace de travail Log Analytics. Utilisez une stratégie qui vous permette de répondre en temps opportun aux problèmes tout en réduisant vos coûts. Votre abonnement sera facturé pour chaque règle d’alerte, comme indiqué dans [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs).

La stratégie recommandée consiste à commencer par deux règles d’alerte en fonction du niveau du problème. Utilisez une fréquence courte, par exemple toutes les 5 minutes, pour les erreurs et une fréquence plus longue, par exemple 24 heures, pour les avertissements. Dans la mesure où les erreurs indiquent une potentielle perte de données, vous pouvez les traiter rapidement pour réduire les pertes. Les avertissements indiquent généralement un problème qui ne requiert pas une attention immédiate. Vous pouvez donc les examiner de façon quotidienne.

Utilisez le processus décrit dans [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../alerts/alerts-log.md) pour créer des alertes de journal. Les sections suivantes décrivent les détails pour chaque règle.


| Requête | Valeur du seuil | Période | Fréquence |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Ces règles d’alerte répondent de la même manière à toutes les opérations indiquant Erreur ou Avertissement. Une fois familiarisé avec les opérations qui génèrent des alertes, vous pouvez répondre différemment à certaines opérations. Par exemple, vous pouvez envoyer des notifications à différentes personnes pour des opérations spécifiques. 

Pour créer une règle d’alerte pour une opération spécifique, utilisez une requête comportant les colonnes **Catégorie** et **Opération**. 

L’exemple suivant crée une alerte Avertissement lorsque le taux de volume d’ingestion atteint 80 % de la limite.

- Cible : Sélectionnez votre espace de travail Log Analytics.
- Critères :
  - Nom du signal : Recherche personnalisée dans les journaux
  - Requête de recherche : `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Basé sur : Nombre de résultats
  - Condition : Supérieur à
  - Seuil : 0
  - Période : 5 (minutes)
  - Fréquence : 5 (minutes)
- Nom de la règle d'alerte : limite de données quotidienne atteinte
- Gravité : avertissement (Sev 1)


L’exemple suivant crée une alerte Avertissement lorsque la collecte de données atteint la limite quotidienne. 

- Cible : Sélectionnez votre espace de travail Log Analytics.
- Critères :
  - Nom du signal : Recherche personnalisée dans les journaux
  - Requête de recherche : `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Basé sur : Nombre de résultats
  - Condition : Supérieur à
  - Seuil : 0
  - Période : 5 (minutes)
  - Fréquence : 5 (minutes)
- Nom de la règle d'alerte : limite de données quotidienne atteinte
- Gravité : avertissement (Sev 1)
 
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de journal](../alerts/alerts-log.md).
- [Collectez des données d’audit de requête](./query-audit.md) pour votre espace de travail.
