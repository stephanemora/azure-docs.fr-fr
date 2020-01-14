---
title: Log Analytics - FAQ | Microsoft Docs
description: Réponses aux questions fréquentes sur le service Azure Monitor Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394784"
---
# <a name="log-analytics-faq"></a>FAQ sur Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cette FAQ Microsoft consiste en une liste de questions fréquentes sur l’espace de travail Azure Monitor Log Analytics. Si vous avez d’autres questions sur Log Analytics, rendez-vous sur le [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.


## <a name="new-logs-experience"></a>Nouvelle expérience de journaux d’activité

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>Q : Quelle est la différence entre la nouvelle expérience de journaux d’activité et Log Analytics ?

A : Il n’y en a pas. [Log Analytics est intégré en tant que fonctionnalité dans Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) pour fournir une expérience de surveillance plus uniforme. La nouvelle expérience de journaux d’activité dans Azure Monitor est strictement identique aux requêtes Log Analytics déjà utilisées par de nombreux clients.

### <a name="q-can-i-still-use-log-search"></a>Q : Puis-je continuer à utiliser Recherche dans les journaux ? 

A : Recherche dans les journaux d’activité est actuellement toujours disponible dans le portail OMS et dans le portail Azure, sous le nom **Journaux d’activité (classique)** . Le portail OMS sera officiellement mis hors-service le 15 janvier 2019. L’expérience de journaux d’activité classique dans le portail Azure sera progressivement retirée et remplacée par la nouvelle expérience de journaux d’activité. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Q. Puis-je continuer à utiliser le portail Advanced Analytics ? 
La nouvelle expérience de journaux d’activité dans le portail Azure est basée sur le portail Advanced Analytics, mais vous pouvez toujours y accéder en dehors du portail Azure. La feuille de route de suppression de ce portail externe sera bientôt annoncée.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Q. Pourquoi ne puis-je pas voir les boutons Explorateur de requêtes et Enregistrer dans la nouvelle expérience de journaux d’activité ?

Les boutons **Explorateur de requêtes**, **Enregistrer** et **Définir l’alerte** ne sont pas disponibles lorsque vous explorez des journaux d’activité dans le contexte d’une ressource spécifique. Pour créer des alertes, enregistrer ou charger une requête, l’étendue des journaux d’activité doit être un espace de travail. Pour ouvrir des journaux d’activité dans le contexte de l’espace de travail, sélectionnez **Tous les services** > **Surveiller** > **Journaux d’activité**. Le dernier espace de travail utilisée est sélectionné, mais vous pouvez sélectionner n’importe quel autre espace de travail. Consultez [Affichage et analyse de données dans Log Analytics](../log-query/portals.md) pour plus d’informations.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Q. Comment extraire des champs personnalisés dans la nouvelle expérience de journaux d’activité ? 

A : L’extraction de champs personnalisée est actuellement prise en charge dans l’expérience de journaux d’activité classique. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Q. Où trouver le mode liste dans les nouveaux journaux d’activité ? 

A : Le mode Liste n’est pas disponible dans les nouveaux journaux d’activité. Il existe une flèche à gauche de chaque enregistrement dans le tableau des résultats. Cliquez sur cette flèche pour ouvrir les détails de l’enregistrement correspondant. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>Q. Après l'exécution d'une requête, une liste de filtres suggérés est disponible. Comment puis-je voir ces filtres ? 

A : Cliquez sur « Filtres » dans le volet de gauche pour afficher un aperçu de la nouvelle implémentation des filtres. Elle est désormais basée sur votre jeu de résultats complet plutôt que restreinte selon la limite de 10 000 enregistrements de l’interface utilisateur. Il s’agit actuellement d’une liste des filtres les plus populaires et des 10 valeurs les plus courantes pour chaque filtre. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Q. Pourquoi l’erreur : « Inscrivez le fournisseur de ressources ’Microsoft.Insights’ pour cet abonnement afin d’activer cette requête » dans les journaux d’activité après une exploration à partir d’une machine virtuelle ? 

A : Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement ; toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Ceci configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Pour plus d’informations, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Q. Pourquoi est-ce que je ne reçois aucun message d’erreur d’accès lorsque j’accède aux journaux d’activité à partir d’une page de machine virtuelle ? 

A : Pour voir les journaux d’activité de machine virtuelle, vous devez disposer de l’autorisation de lecture sur les espaces de travail qui les stockent. Dans ce cas, votre administrateur doit vous accorder des autorisations dans Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Q. Pourquoi puis-je accéder à mon espace de travail dans le portail OMS, mais que je reçois l’erreur « Vous n’avez aucun accès » dans le portail Azure ?  

A : Pour accéder à un espace de travail dans Azure, des autorisations Azure doivent vous être assignées. Dans certains cas, vous ne disposerez peut-être pas des autorisations d’accès appropriées. Dans ce cas, votre administrateur doit vous accorder des autorisations dans Azure. Consultez [Migration du portail OMS vers Azure](oms-portal-transition.md) pour plus d’informations.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Q. Pourquoi ne puis-je pas voir l’entrée Concepteur de vues dans les journaux d’activité ?

A : Le Concepteur de vues est disponible dans les journaux d’activité seulement pour les utilisateurs auxquels des autorisations Contributeur ou supérieures ont été assignées.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Q. Puis-je continuer à utiliser le portail Analytics en dehors d’Azure ?

R. Oui, la page Journaux d’activité dans Azure et le portail Advanced Analytics reposent sur le même code. Log Analytics est intégré en tant que fonctionnalité dans Azure Monitor pour fournir une expérience de supervision plus uniforme. Vous pouvez encore accéder au portail Analytics via l’URL : https:\/\/portal.loganalytics.io/subscriptions/{IDabonnement}/resourcegroups/{nomGroupeRessources}/workspaces/{nomEspaceTravail}.



## <a name="general"></a>Général

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Q. Comment puis-je voir mes vues et solutions dans le portail Azure ? 

A : Les listes des vues et des solutions installées sont disponibles dans le portail Azure. Cliquez sur **Tous les services**. Dans la liste des ressources, sélectionnez **Surveiller**, puis cliquez sur **...Plus**. Le dernier espace de travail utilisée est sélectionné, mais vous pouvez sélectionner n’importe quel autre espace de travail. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics utilise-t-il le même agent qu’Azure Security Center ?

A : Au début du mois de juin 2017, Azure Security Center a commencé à utiliser Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Forum aux questions sur la migration de plateforme Azure Security Center](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. Quels sont les contrôles effectués par les solutions AD et SQL Assessment ?

A : La requête suivante comporte une description de tous les contrôles effectués actuellement :

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Les résultats peuvent ensuite être exportés vers Excel pour être examinés.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q. Pourquoi vois-je autre chose qu’OMS dans la console System Center Operations Manager ?

A : Selon le correctif cumulatif d’Operations Manager que vous utilisez, vous pouvez voir un nœud pour *System Center Advisor*, *Operational Insights* ou *Log Analytics*.

La mise à jour de la chaîne de texte vers *OMS* est incluse dans un pack d’administration, qui doit être importé manuellement. Pour afficher le texte et les fonctionnalités actuels, suivez les instructions de l’article de la Base de connaissances sur le dernier correctif cumulatif de System Center Operations Manager et actualisez la console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q : Existe-t-il une version locale de Log Analytics ?

A : Non. Log Analytics est un service cloud évolutif qui traite et stocke d’importants volumes de données. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. Comment être informé de l’arrêt de la collecte de données ?

A : Suivez les étapes décrites dans [Créer une nouvelle alerte de journal](../../azure-monitor/platform/alerts-metric.md) pour être averti lorsque la collecte de données s’arrête.

Lorsque vous créez l’alerte d’arrêt de la collecte de données, définissez les valeurs suivantes :

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche personnalisée dans les journaux**.
   - **Requête de recherche** sur `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - La **logique d’alerte** est **basée sur** le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *30* minutes et **Fréquence d’alerte** toutes les *10* minutes
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** : *Collecte de données arrêtée*
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](../../azure-monitor/platform/action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé si vous disposez d’une pulsation manquante pendant plus de 15 minutes.

## <a name="configuration"></a>Configuration

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Puis-je modifier le nom du conteneur Blob ou Table utilisé pour lire sur Diagnostics Azure (WAD) ?

R. Non, il n’est pas possible pour le moment de lire des tables ou des conteneurs arbitraires dans le stockage Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Quelles sont les adresses IP utilisées par le service Log Analytics ? Comment vérifier que le pare-feu autorise uniquement le trafic vers le service Log Analytics ?

R. Le service Log Analytics repose sur Azure. Les adresses IP Log Analytics se trouvent dans les [plages d’adresses IP des centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Les adresses IP réelles du service Log Analytics changent au fil des déploiements. Les noms DNS à autoriser via votre pare-feu sont documentés dans la [configuration réseau requise](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. J’utilise ExpressRoute pour me connecter à Azure. Mon trafic Log Analytics utilise-t-il ma connexion ExpressRoute ?

R. Les différents types de trafic ExpressRoute sont décrits dans la [documentation ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Le trafic vers Log Analytics utilise le circuit ExpressRoute d’homologation publique.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. Existe-t-il un moyen simple de déplacer un espace de travail Log Analytics existant vers un autre espace de travail Log Analytics ou un autre abonnement Azure ?

R. La cmdlet `Move-AzResource` permet de déplacer un espace de travail Log Analytics ainsi qu’un compte Automation d’un abonnement Azure à un autre. Pour en savoir plus, voir [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Cette modification peut également être effectuée dans le portail Azure.

Vous ne pouvez pas déplacer les données d’un espace de travail Log Analytics vers un autre ou modifier la région de données dans laquelle les données Log Analytics sont stockées.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q : Comment ajouter Log Analytics à System Center Operations Manager ?

A :  R : La mise à jour vers le dernier correctif cumulatif et l’importation de packs d’administration permettent de connecter Operations Manager à Log Analytics.

>[!NOTE]
>La connexion d’Operations Manager à Log Analytics n’est disponible que pour System Center Operations Manager 2012 SP1 et les versions ultérieures.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q : Comment puis-je vérifier qu’un agent est en mesure de communiquer avec Log Analytics ?

A : Pour que l’agent puisse communiquer avec l’espace de travail Log Analytics, accédez à : Panneau de configuration, Sécurité et paramètres, **Microsoft Monitoring Agent**.

Sous l’onglet **Azure Log Analytics (OMS)** , recherchez une coche verte. Une icône en forme de coche verte confirme que l’agent est en mesure de communiquer avec le service Azure.

Une icône d’avertissement jaune signifie que l’agent rencontre des problèmes de communication avec Log Analytics. L’une des raisons courantes est que le service Microsoft Monitoring Agent s’est arrêté. Utilisez le Gestionnaire de contrôle des services pour redémarrer le service.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q : Comment interrompre la communication d’un agent avec Log Analytics ?

A : Dans System Center Operations Manager, supprimez l’ordinateur de la liste des ordinateurs gérés par Log Analytics. Operations Manager met à jour la configuration de l’agent pour qu’il ne fasse plus de rapports à Log Analytics. En ce qui concerne les agents connectés directement à Log Analytics, vous pouvez les empêcher de communiquer de la façon suivante : Panneau de configuration, Sécurité et paramètres, **Microsoft Monitoring Agent**.
Sous **Azure Log Analytics (OMS)** , supprimez tous les espaces de travail répertoriés.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q : Pourquoi j’obtiens une erreur quand j’essaie de déplacer mon espace de travail d’un abonnement Azure vers un autre ?

A : Pour déplacer un espace de travail vers un autre abonnement ou groupe de ressources, vous devez d’abord dissocier le compte Automation dans l’espace de travail. La dissociation d’un compte Automation nécessite la suppression de ces solutions si elles sont installées dans l’espace de travail : Update Management, Change Tracking ou Start/Stop VMs during off-hours sont supprimées. Une fois ces solutions supprimées, dissociez le compte Automation en sélectionnant **espaces de travail associé** sur le volet gauche dans ressource du compte Automation, puis cliquez sur **dissocier l’espace de travail** sur le ruban.
 > Les solutions supprimées doivent être réinstallées dans l’espace de travail et le compte Automation associé à l’espace de travail doit être remis en l’état après le déplacement.

Vérifiez que vous disposez de l’autorisation nécessaire dans les deux abonnements Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Q : Pourquoi j’obtiens une erreur quand j’essaie de mettre à jour une recherche enregistrée ?

A : Vous devez ajouter 'etag' au corps de l’API, ou les propriétés du modèle Azure Resource Manager :
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Données de l’agent
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. Quelle quantité de données puis-je envoyer via l’agent à Log Analytics ? Existe-t-il une quantité maximale de données par client ?
R. Il n’existe aucune limite quant à la quantité de données chargées ; elle dépend de l’option de tarification sélectionnée (réservation de capacité ou paiement à l’utilisation). Un espace de travail Log Analytics est conçu pour monter en puissance (scale up) automatiquement afin de traiter le volume provenant du client, même si cela se chiffre en téraoctets par jour. Pour plus d’informations, voir [Informations tarifaires](https://azure.microsoft.com/pricing/details/monitor/).

L’agent Log Analytics a été conçu pour garantir un faible encombrement. Le volume de données varie selon les solutions activées. Vous trouverez des informations détaillées sur le volume de données ainsi que la répartition par solution sur la page [Utilisation](../../azure-monitor/platform/data-usage.md).

Pour plus d’informations, vous pouvez lire un [blog de client](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) affichant les résultats après évaluation de l’utilisation des ressources (empreinte) de l’agent Log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. Quelle est la quantité de bande passante réseau utilisée par Microsoft Management Agent (MMA) lorsqu’il envoie des données à Log Analytics ?

R. La bande passante est fonction de la quantité de données envoyées. Les données sont compressées à mesure de leur envoi sur le réseau

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. Quelle est la quantité de données envoyées par agent ?

R. La quantité de données envoyées par agent dépend :

* des solutions activées ;
* du nombre de journaux d’activité et de compteurs de performances collectés ;
* du volume de données des journaux d’activité.

L’utilisation globale est indiquée sur la page [Utilisation](../../azure-monitor/platform/data-usage.md) .

Pour les ordinateurs en mesure d’exécuter l’agent Wire Data, lancez la requête suivante afin de connaître la quantité de données envoyées :

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Étapes suivantes

[Bien démarrer avec Azure Monitor](../../azure-monitor/overview.md) pour en savoir plus sur Log Analytics et être opérationnel en quelques minutes.
