---
title: Résoudre les problèmes liés aux sorties Azure Stream Analytics
description: Cet article décrit des techniques pour résoudre les problèmes liés aux connexions de sortie dans les travaux Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228025"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Résoudre les problèmes liés aux sorties Azure Stream Analytics

Cette page décrit les problèmes courants avec les connexions de sortie et comment les résoudre.

## <a name="output-not-produced-by-job"></a>Sortie non produite par un travail
1.  Vérifiez la connectivité aux sorties à l’aide du bouton **Tester la connexion** pour chaque sortie.

2.  Consultez les [**métriques de surveillance**](stream-analytics-monitoring.md) dans l’onglet **Surveiller**. Comme les valeurs sont agrégées, les métriques sont différées de quelques minutes.
    - Si le nombre d’événements d’entrée n’est pas nul, le travail peut lire les données. Si le nombre d’événements d’entrée est nul, alors :
      - Vérifiez si la source de données comporte des données valides à l’aide de [l’Explorateur Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Cette vérification s’applique si le travail utilise le concentrateur d’événements comme entrée.
      - Vérifiez si les formats de sérialisation et d’encodage des données sont respectés.
      - Si le travail utilise un concentrateur d’événements, vérifiez si le corps du message est *Null*.

    - Si le nombre d’erreurs de conversion de données n’est pas nul et augmente, plusieurs causes sont possibles :
      - L’événement de sortie n’est pas conforme au schéma du récepteur cible.
      - Le schéma d’événements ne correspond peut-être pas au schéma défini ou attendu des événements de la requête.
      - Les types de données de certains champs de l’événement ne correspondent peut-être pas aux types attendus.

    - Si le nombre d’erreurs d’exécution n’est pas nul, cela signifie que le travail peut recevoir les données, mais que des erreurs se produisent durant le traitement de la requête.
      - Pour trouver les erreurs, accédez aux [journaux d’activité d’audit](../azure-resource-manager/management/view-activity-logs.md) et filtrez sur l’état *Échec*.

    - Si le nombre d’événements d’entrée n’est pas nul et que le nombre d’événements de sortie est nul, cela peut s’expliquer par l’une des raisons suivantes :
      - Le traitement de la requête a abouti à un nombre nul d’événements de sortie.
      - Les événements ou leurs champs peuvent être formés de manière inappropriée et n’entraîner aucune sortie après le traitement des requêtes.
      - Le travail n’a pas pu envoyer (push) de données au récepteur de sortie pour des raisons d’authentification ou de connectivité.

    - Dans tous ces cas d’erreur, les messages des journaux d’opérations donnent des détails supplémentaires (notamment sur le déroulé des événements), sauf lorsque la logique de requête a filtré l’ensemble des événements. Si le traitement de plusieurs événements génère des erreurs, Stream Analytics consigne les 3 premiers messages d’erreur du même type dans un intervalle de 10 minutes dans les journaux d’activité des opérations. Il supprime ensuite toutes les autres erreurs identiques avec un message indiquant que les erreurs survenant trop rapidement sont supprimées.

## <a name="job-output-is-delayed"></a>La sortie du travail est retardée

### <a name="first-output-is-delayed"></a>La première sortie est retardée
Lorsqu’un travail Stream Analytics est démarré, les événements d’entrée sont lues, mais il peut y avoir un retard dans la sortie produite dans certaines circonstances.

Les valeurs de temps importantes dans les éléments de requête temporelle peuvent contribuer au retard de la sortie. Pour produire une sortie correcte sur les grandes fenêtres de temps, le travail de diffusion en continu démarre par la lecture des données à partir du moment le plus récent possible (jusqu'à sept jours) pour remplir la fenêtre de temps. Pendant ce temps, aucune sortie n’est générée jusqu'à ce que la lecture de rattrapage des événements d’entrée en attente soit terminée. Ce problème peut apparaître lorsque le système met à niveau les travaux de diffusion en continu, puis redémarre le travail. Ces mises à niveau sont généralement effectuées une fois tous les deux mois.

Par conséquent, faites preuve de discernement lors de la conception d’une requête Stream Analytics. Si vous utilisez une grande fenêtre de temps (plus de plusieurs heures, jusqu'à sept jours) pour les éléments temporels dans la syntaxe de requête du travail, un retard de la première sortie peut survenir lorsque le travail est démarré ou redémarré.  

Une manière d’atténuer les risques pour ce type de retard consiste à utiliser des techniques de parallélisation des requêtes (partitionnement des données) ou à ajouter plus d’unités de streaming pour améliorer le débit jusqu'à ce que le travail rattrape son retard.  Pour plus d’informations, consultez [Considérations relatives à la création de travaux Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Ces facteurs ont un impact sur la rapidité d'exécution de la première sortie générée :

1. Utiliser des agrégats fenêtrés (GROUP BY de fenêtres de Bascule, Récurrente et Glissante)
   - Pour les agrégats de fenêtres Bascule ou Récurrente, les résultats sont générés à la fin de la plage de temps de la fenêtre.
   - Pour une fenêtre Glissante, les résultats sont générés lorsqu’un événement entre ou quitte la fenêtre Glissante.
   - Si vous envisagez d’utiliser de grandes fenêtres (> 1 heure), il est conseillé de choisir la fenêtre Récurrente ou Glissante afin de pouvoir voir la sortie plus fréquemment.

2. Utiliser les jointures temporelles (JOIN avec DATEDIFF)
   - Des correspondances sont générées dès que les deux côtés des événements correspondants surviennent.
   - Les données qui ne disposent pas d’une correspondance (JOINTURE EXTERNE GAUCHE) sont générées à la fin de la fenêtre DATEDIFF par rapport à chaque événement sur le côté gauche.

3. Utiliser des fonctions analytiques temporelles (ISFIRST, LAST et LAG avec LIMIT DURATION)
   - Pour les fonctions analytiques, la sortie est générée pour chaque événement, il n’existe pas de retard.

### <a name="output-falls-behind"></a>La sortie prend du retard
Lors du fonctionnement normal du travail, si vous trouvez que la sortie du travail prend du retard (latence de plus en plus longue), vous pouvez identifier les causes de base en examinant les facteurs suivants :
- si le récepteur en aval est limité
- si la source en amont est limitée
- si la logique de traitement de la requête nécessite beaucoup de ressources système

Pour afficher les détails, dans le Portail Azure, sélectionnez le travail de diffusion en continu, puis sélectionnez le **diagramme de travail**. Pour chaque entrée, il existe une métrique d’événements de backlog par partition. Si la métrique d’événements de backlog continue à augmenter, cela indique que les ressources système sont limitées. Ceci est potentiellement dû à la limitation du récepteur de sortie ou une UC élevée. Pour plus d’informations sur l’utilisation du diagramme de travail, consultez [Débogage piloté par les données à l’aide du diagramme de travail](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Avertissement de violation de clé avec la sortie Azure SQL Database

Quand vous configurez Azure SQL Database comme sortie d’une tâche Stream Analytics, les enregistrements sont insérés en bloc dans la table de destination. En règle générale, Azure Stream Analytics garantit [au moins une remise](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) dans le récepteur de sortie, mais vous pouvez toujours [obtenir exactement une remise]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) dans la sortie SQL quand une contrainte unique est définie pour la table SQL.

Une fois les contraintes clés uniques configurées sur la table SQL et que des enregistrements en double sont insérés dans la table SQL, Azure Stream Analytics supprime l’enregistrement en double. Il fractionne les données en lots et insère les lots de manière récursive jusqu'à trouver un seul enregistrement en double. Si le travail de diffusion en continu a un nombre considérable de lignes en double, cette division et le processus d’insertion doit ignorer les doublons, un par un, ce qui est moins efficace et prend plus de temps. Si vous voyez plusieurs messages d’avertissement de violation de clé dans votre journal d’activité au cours de l’heure passée, il est probable que votre sortie SQL ralentit l’intégralité de la tâche.

Pour résoudre ce problème, vous devez [configurer l’index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) qui provoque la violation de clé en activant l’option IGNORE_DUP_KEY. L’activation de cette option permet à SQL d’ignorer les valeurs en double au cours des insertions en bloc et SQL Azure génère simplement un message d’avertissement au lieu d’une erreur. Azure Stream Analytics ne génère plus d’erreurs de violation de clé primaire.

Notez les observations suivantes lors de la configuration d’IGNORE_DUP_KEY pour plusieurs types d’index :

* Vous ne pouvez pas définir IGNORE_DUP_KEY sur une clé primaire ni une contrainte unique qui utilise ALTER INDEX ; vous devez supprimer et recréer l’index.  
* Vous pouvez définir l’option IGNORE_DUP_KEY avec ALTER INDEX pour un index unique, qui est différent de la contrainte PRIMARY KEY/UNIQUE et créé à l’aide de la définition CREATE INDEX ou INDEX.  
* IGNORE_DUP_KEY ne s’applique pas aux index de stockage de colonnes, car vous ne pouvez pas imposer l’unicité sur ces derniers.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Le nom des colonnes est mis en minuscules par Azure Stream Analytics
Avec le niveau de compatibilité d’origine (1.0), Azure Stream Analytics passait le nom des colonnes en minuscules. Ce comportement a été résolu dans les niveaux de compatibilité ultérieurs. Pour préserver la casse, nous conseillons aux clients de passer au niveau de compatibilité 1.1 ou version ultérieure. Pour plus d’informations, consultez [Comprendre le niveau de compatibilité pour les travaux Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
