---
title: Gérer le coût des données dans Azure Log Analytics | Microsoft Docs
description: Découvrez comment changer le plan tarifaire et gérer la stratégie de rétention et de volume des données de votre espace de travail Log Analytics dans Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2018
ms.author: magoedte
ms.openlocfilehash: 43f7f1160fa36745bcfd697d91d1b46615b99edc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637512"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Gérer les coûts en contrôlant le volume et la conservation des données dans Log Analytics
Log Analytics est conçu pour la mise à l’échelle et la prise en charge de la collecte, de l’indexation et du stockage de quantités importantes de données quotidiennes provenant de toute source de votre entreprise ou déployées dans Azure.  Si ce peut être un élément moteur pour votre organisation, la rentabilité est au final ce qui importe le plus. À cette fin, il est important de comprendre que le coût d’un espace de travail Log Analytics n’est pas simplement fonction du volume des données collectées, mais qu’il dépend aussi du plan sélectionné et de la durée de stockage des données générées à partir de vos sources connectées.  

Dans cet article, nous allons passer en revue les méthodes permettant de surveiller de façon proactive la croissance du stockage et du volume de données, et définir des limites pour contrôler les coûts associés. 

Le coût des données peut être considérable en fonction des facteurs suivants : 

- Nombre de systèmes, de composants d’infrastructure, de ressources cloud, etc., à partir desquels vous effectuez des collectes 
- Type de données créées par la source, telles que des files d’attente de messages, des journaux, des événements, des données relatives à la sécurité ou des métriques de performances 
- Volume de données généré par ces sources et ingéré dans l’espace de travail 
- Données de la période conservées dans l’espace de travail  
- Nombre de solutions de gestion activées, source de données et fréquence des collectes 

> [!NOTE]
> Consultez la documentation de chaque solution, car elle fournit une estimation de la quantité de données collectée.   

Les clients avec un Contrat Entreprise signé avant le 1er juillet 2018 ou qui ont déjà créé un espace de travail Log Analytics dans un abonnement ont toujours accès au plan *Gratuit*. Si votre abonnement n’est pas lié à une inscription de Contrat Entreprise existante, le niveau *Gratuit* n’est pas disponible quand vous créez un espace de travail dans un nouvel abonnement après le 2 avril 2018.  La conservation des données est limitée à 7 jours pour le niveau *Gratuit*.  Pour les niveaux *Autonome* ou *Payant*, les données collectées sont disponibles pendant les 31 derniers jours. Le niveau *Gratuit* a une limite d’ingestion des données quotidienne de 500 Mo. Si vous constatez que vous dépassez constamment le volume autorisé, vous pouvez changer votre espace de travail pour un plan payant permettant de collecter des données au-delà de cette limite. 

> [!NOTE]
> Des frais s’appliquent si vous choisissez de sélectionner une période de rétention plus longue pour le niveau payant. Vous pouvez changer de type de plan à tout moment ; pour plus d’informations sur les tarifs, consultez les [détails des tarifs](https://azure.microsoft.com/pricing/details/log-analytics/). 

Deux méthodes permettent de limiter le volume de données et de contrôler le coût : la conservation des données et la limite quotidienne.  

## <a name="review-estimated-cost"></a>Vérifier l’estimation du coût
Log Analytics permet d’estimer facilement les coûts en fonction des modèles d’utilisation récente.  Pour ce faire, procédez comme suit.  

1. Connectez-vous au [portail Azure](http://portal.azure.com). 
2. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.<br><br> ![Portail Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Dans le volet d’abonnements de Log Analytics, sélectionnez votre espace de travail, puis cliquez sur **Utilisation et estimation des coûts** dans le volet gauche.<br><br> ![Page Utilisation et estimation des coûts](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

C’est ici que vous pouvez consulter votre volume de données pour le mois. Ce volume inclut toutes les données reçues et conservées dans votre espace de travail Log Analytics.  En haut de la page, cliquez sur **Détails d’utilisation** pour afficher le tableau de bord d’utilisation contenant des informations sur les tendances de volume de données par source, ordinateur et offre. Pour afficher et définir une limite quotidienne ou pour modifier la période de rétention, cliquez sur **Gestion du volume de données**.
 
Les frais liés à Log Analytics sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure sont affichées dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite quotidienne
Lorsque vous créez un espace de travail Log Analytics à partir du portail Azure et que vous choisissez le plan *Gratuit*, il est défini sur une limite de 500 Mo par jour. Aucune limite n’est définie pour les autres plans de tarification. Vous pouvez configurer une limite quotidienne et restreindre l’ingestion quotidienne de votre espace de travail, mais soyez vigilant, car votre objectif n’est pas d’atteindre la limite quotidienne.  Si vous l’atteignez, vous perdrez des données pour le reste de la journée, ce qui peut impacter les autres services et solutions Azure dont les fonctionnalités dépendent de la disponibilité de données à jour dans l’espace de travail.  Ces fonctionnalités peuvent correspondre, par exemple, à votre capacité à observer et à recevoir des alertes lorsque les conditions d’intégrité des ressources de service informatique sont impactées.  La limite quotidienne est destinée à être utilisée comme un moyen de gérer l’augmentation inattendue du volume de données de vos ressources managées ou lorsque vous souhaitez simplement limiter les frais non planifiés de votre espace de travail.  

Lorsque cette limite quotidienne est atteinte, la collecte des types de données facturables s’arrête pour le reste de la journée. Une bannière d’avertissement s’affiche en haut de la page de l’espace de travail Log Analytics sélectionné, et un événement d’opération est envoyé vers la table *Opération* dans la catégorie **LogManagement**. La collecte de données reprend après l’heure de réinitialisation définie dans *La limite quotidienne est fixée à*. Nous vous recommandons de définir une règle d’alerte en fonction de cet événement d’opération, configuré pour avertir lorsque la limite de données quotidienne a été atteinte. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifier la limite de données quotidienne à définir 
Consultez [Utilisation et estimation des coûts Log Analytics](log-analytics-usage.md) pour comprendre les tendances d’ingestion des données et la limite quotidienne de volume à définir. Effectuez cette opération avec précaution, car vous ne pourrez plus surveiller vos ressources, une fois que la limite sera atteinte. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gérer le volume de données maximal quotidien 
Les étapes suivantes décrivent la configuration d’une limite pour gérer le volume des données ingérées quotidiennement par Log Analytics.  

1. À partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. Cliquez sur **Gestion du volume de données** en haut de la page **Utilisation et estimation des coûts** de l’espace de travail sélectionné. 
5. Par défaut, la limite quotidienne est **DÉSACTIVÉE** : cliquez sur **ACTIVER** pour l’activer, puis définissez la limite de volume de données en Go/jour.<br><br> ![Configurer la limite de données dans Log Analytics](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Alerte lorsque la limite est atteinte
Si nous présentons un indice visuel dans le portail Azure lorsque le seuil limite des données est atteint, ce comportement n’est pas nécessairement en harmonie avec la façon dont vous gérez les problèmes opérationnels exigeant une attention immédiate.  Pour recevoir une notification d’alerte, vous pouvez créer une règle d’alerte dans Azure Monitor.  Pour plus d’informations, reportez-vous à l’article sur [la création, l’affichage et la gestion des alertes](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Pour vous aider à démarrer, voici les paramètres que nous recommandons pour l’alerte :

* Cible : sélectionnez votre ressource Log Analytics.
* Critères : 
   * Nom du signal : recherche dans les journaux personnalisée
   * Requête de recherche : Operation | where Detail has 'OverQuota'
   * Basé sur : nombre de résultats
   * Condition : supérieur à
   * Seuil : 0
   * Période : 5 (minutes)
   * Fréquence : 5 (minutes)
* Nom de la règle d’alerte : limite de données quotidienne atteinte
* Gravité : avertissement (gravité 1)

Une fois que l’alerte est définie et que la limite est atteinte, l’alerte est déclenchée et effectue la réponse définie dans le groupe d’actions. Elle peut informer votre équipe via des e-mails et des SMS ou automatiser des actions à l’aide de Webhooks ou de runbooks Automation, ou [en s’intégrant à une solution ITSM externe](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Changer la période de rétention des données 
Les étapes suivantes décrivent la configuration de la durée de conservation des données de journal dans votre espace de travail.
 
1. À partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. Cliquez sur **Gestion du volume de données** en haut de la page **Utilisation et estimation des coûts**.
5. Dans le volet, déplacez le curseur pour augmenter ou diminuer le nombre de jours, puis cliquez sur **OK**.  Si vous avez opté pour le niveau *Gratuit*, vous ne pouvez pas modifier la période de rétention de données et vous devez passer au niveau payant afin de contrôler ce paramètre.<br><br> ![Changer le paramètre de rétention de données de l’espace de travail](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Résolution de problèmes
**Question** : comment procéder au dépannage si Log Analytics ne collecte plus de données ? 
**Réponse** : si vous utilisez le niveau tarifaire gratuit et que vous avez envoyé plus de 500 Mo de données le même jour, la collecte de données s’arrête pour le reste de la journée. La limite quotidienne est la principale raison pour laquelle Log Analytics arrête la collecte de données ou des données semblent manquantes.  
Log Analytics crée un événement de type Opération lorsque la collecte de données démarre et s’arrête.  
Exécutez la requête suivante dans la recherche pour vérifier si vous atteignez la limite quotidienne et si des données sont manquantes : Operation | where OperationCategory == 'Data Collection Status'   
Lorsque la collecte de données s’arrête, le paramètre OperationStatus a la valeur Warning. Lorsque la collecte de données démarre, le paramètre OperationStatus a la valeur Succeeded.  
Le tableau suivant décrit les raisons pour lesquelles la collecte de données s’arrête et suggère une action pour la reprendre :  

|Raison pour laquelle la collecte s’arrête| Solution| 
|-----------------------|---------|
|Limite quotidienne de données gratuites atteinte<sup>1</sup>|Attendez le jour suivant pour que la collecte redémarre automatiquement ou passez à un niveau tarifaire payant.|
|Limite quotidienne définie dans Gestion du volume de données atteinte|Attendez le jour suivant pour que la collecte redémarre automatiquement ou augmentez la limite du volume de données quotidien décrite dans la section [Gérer le volume de données maximal quotidien](#manage-the-maximum-daily-volume).|
|Abonnement Azure à l’état interrompu pour la raison suivante :<br> Fin de l’essai gratuit<br> Expiration du Pass Azure<br> Limite de dépense mensuelle atteinte (par exemple, sur un abonnement MSDN ou Visual Studio)|Passer à un abonnement payant<br> Supprimer la limite ou attendre sa réinitialisation|

<sup>1</sup> Si votre espace de travail utilise le niveau tarifaire gratuit, vous êtes limité à 500 Mo de données envoyées au service par jour. Lorsque vous atteignez la limite quotidienne, la collecte de données s’arrête jusqu’au jour suivant. Les données envoyées pendant l’arrêt de la collecte de données ne sont pas indexées et ne sont pas accessibles à la recherche. Lorsque la collecte de données reprend, le traitement se produit uniquement pour les nouvelles données envoyées. 

Log Analytics utilise l’heure UTC. L’heure de réinitialisation varie entre les espaces de travail pour empêcher tous ceux qui sont limités de démarrer simultanément l’ingestion de données. Si l’espace de travail atteint la limite quotidienne, le traitement reprend après l’heure de réinitialisation définie dans **La limite quotidienne est fixée à**.<br><br> ![Fuseau horaire UTC de la limite dans Log Analytics](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Question** : comment être informé de l’arrêt de la collecte de données ? 
**Réponse** : utilisez les étapes décrites dans l’alerte *Créer une limite quotidienne de données* pour être averti de l’arrêt de la collecte de données, puis suivez les étapes décrites dans la section relative à la configuration d’une action par e-mail, Webhook ou runbook pour la règle d’alerte. 

## <a name="next-steps"></a>Étapes suivantes  

Pour déterminer la quantité de données collectée, les sources qui les envoient et les différents types de données envoyées pour faciliter la gestion de la consommation et des coûts, consultez [Analyser l’utilisation des données dans Log Analytics](log-analytics-usage.md).