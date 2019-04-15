---
title: Problèmes courants à résoudre dans Azure Stream Analytics
description: Cet article décrit plusieurs problèmes courants dans Azure Stream Analytics et les étapes à suivre pour résoudre ces problèmes.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521473"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problèmes courants dans Stream Analytics et étapes de dépannage

## <a name="troubleshoot-malformed-input-events"></a>Résoudre les problèmes d’événements d’entrée incorrectement formés

 Les problèmes de sérialisation proviennent de la présence de messages incorrectement formés dans le flux d’entrée de votre travail Stream Analytics. Par exemple, un message incorrectement formé peut être provoqué par une parenthèse ou une accolade manquante dans un objet JSON ou par un format d’horodatage incorrect dans un champ d’heure. 
 
 Lorsqu’un travail Stream Analytics reçoit un message incorrectement formé d’une entrée, il dépose le message et envoie un avertissement à l’utilisateur. Un symbole d’avertissement s’affiche dans la vignette **Entrées** de votre tâche Stream Analytics. (Ce signe d’avertissement existe tant que la tâche est en cours d’exécution) :

![Vignette d’entrées sur le tableau de bord Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Pour plus d’informations, activez les journaux de diagnostics pour afficher les détails de l’avertissement. Pour les événements d’entrée incorrectement formés, les journaux d’activité d’exécution contiennent une entrée avec un message semblable à celui-ci : « Message : Impossible de désérialiser l’ou les événements d’entrée à partir de la ressource \<URI d’objet blob > au format json ». 

### <a name="troubleshooting-steps"></a>Étapes de dépannage

1. Accédez à la vignette Entrées, puis cliquez dessus pour afficher les avertissements.

2. La vignette Détails de l’entrée affiche un ensemble d’avertissements avec des informations détaillées sur le problème. Voici un exemple de message d’avertissement qui indique la partition, le décalage et les numéros de séquence où se trouvent des données JSON mal formées. 

   ![Message d’avertissement d’entrée avec décalage](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Pour obtenir les données JSON dont le format est incorrect, exécutez le code CheckMalformedEvents.cs. Cet exemple est disponible dans le [référentiel des exemples GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ce code lit l’ID de la partition et le décalage, puis imprime les données situées dans ce décalage. 

4. Une fois que vous avez lu les données, vous pouvez analyser et corriger le format de sérialisation.

5. Vous pouvez également [lire les événements à partir d’un IoT Hub avec l’Explorateur Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Sortie retardée

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

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Gérer les enregistrements en double dans la sortie Azure SQL Database

Quand vous configurez Azure SQL Database comme sortie d’une tâche Stream Analytics, les enregistrements sont insérés en bloc dans la table de destination. En règle générale, Azure Stream Analytics garantit [au moins une remise]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) dans le récepteur de sortie, mais vous pouvez toujours [obtenir exactement une remise]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) dans la sortie SQL quand une contrainte unique est définie pour la table SQL. 

Une fois les contraintes clés uniques configurées sur la table SQL et que des enregistrements en double sont insérés dans la table SQL, Azure Stream Analytics supprime l’enregistrement en double. Il fractionne les données en lots et insère les lots de manière récursive jusqu'à trouver un seul enregistrement en double. Si le travail de diffusion en continu a un nombre considérable de lignes en double, cette division et le processus d’insertion doit ignorer les doublons, un par un, ce qui est moins efficace et prend plus de temps. Si vous voyez plusieurs messages d’avertissement de violation de clé dans votre journal d’activité au cours de l’heure passée, il est probable que votre sortie SQL ralentit l’intégralité de la tâche. 

Pour résoudre ce problème, vous devez [configurer l’index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) qui provoque la violation de clé en activant l’option IGNORE_DUP_KEY. L’activation de cette option permet à SQL d’ignorer les valeurs en double au cours des insertions en bloc et SQL Azure génère simplement un message d’avertissement au lieu d’une erreur. Azure Stream Analytics ne génère plus d’erreurs de violation de clé primaire.

Notez les observations suivantes lors de la configuration d’IGNORE_DUP_KEY pour plusieurs types d’index :

* Vous ne pouvez pas définir IGNORE_DUP_KEY sur une clé primaire ni une contrainte unique qui utilise ALTER INDEX ; vous devez supprimer et recréer l’index.  
* Vous pouvez définir l’option IGNORE_DUP_KEY avec ALTER INDEX pour un index unique, qui est différent de la contrainte PRIMARY KEY/UNIQUE et créé à l’aide de la définition CREATE INDEX ou INDEX.  
* IGNORE_DUP_KEY ne s’applique pas aux index de stockage de colonnes, car vous ne pouvez pas imposer l’unicité sur ces derniers.  

## <a name="next-steps"></a>Étapes suivantes
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
