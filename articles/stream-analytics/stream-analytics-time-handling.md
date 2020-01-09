---
title: Comprendre la gestion du temps dans Azure Stream Analytics
description: Découvrez comment la gestion du temps fonctionne dans Azure Stream Analytics, notamment comment choisir le meilleur moment de début, comment gérer les événements en tardfs et précoces, et les métriques de gestion du temps.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354645"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Comprendre la gestion du temps dans Azure Stream Analytics

Dans cet article, nous examinons les choix de conception que vous pouvez effectuer pour résoudre les problèmes pratiques de la gestion du temps dans le service Azure Stream Analytics. Les choix de conception en matière de gestion du temps sont étroitement liés à des facteurs d’ordre des événements.

## <a name="background-time-concepts"></a>Concepts de base en matière de temps

Pour mieux établir le cadre de la discussion, définissons certains concepts de base :

- **Heure de l’événement** : heure à laquelle l’événement d’origine s’est produit. Il peut s’agir, par exemple, du moment où une voiture s’est approchée d’un poste de péage.

- **Temps de traitement** : moment où l’événement atteint le système de traitement et est observé. Par exemple, il s’agit du laps de temps entre le moment où le capteur du poste péage détecte la voiture et où le système informatique finit de traiter les données.

- **Limite** : marqueur d’heure d’événement qui indique jusqu’à quel point des événements ont été entrés dans le processeur de diffusion en continu. Les limites permettent au système d’indiquer une nette progression de l’ingestion des événements. Compte tenu de la nature des flux, les données d’événements entrants ne s’arrêtent jamais. De ce fait, les limitent indiquent la progression jusqu’à un certain point dans le flux.

   Le concept de limite est important. Les limites permettent à Stream Analytics de déterminer à quel moment le système peut générer des résultats complets, corrects et répétables qu’il n’est pas utile de retirer. Le traitement peut être effectué de façon garantie, prévisible et répétable. Par exemple, s’il est nécessaire d’effectuer un nouveau calcul dans le cadre de la gestion des erreurs, les limites constituent des points de départ et d’arrivée fiables.

Pour en savoir plus sur la sujet, consultez les billets du blog de Tyler Akidau [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) et [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Choix de la meilleure heure de début

Stream Analytics propose aux utilisateurs deux choix en ce qui concerne l’heure de l’événement :

1. **Heure d’arrivée**  

   Une heure d’arrivée est affectée à la source d’entrée quand l’événement atteint la source. Vous pouvez accéder à l’heure d’arrivée à l’aide de la propriété **EventEnqueuedUtcTime** pour les entrées Event Hubs, de la propriété **IoTHub.EnqueuedTime** pour IoT Hub et de la propriété **BlobProperties.LastModified** pour l’entrée d’objet blob.

   L’utilisation de l’heure d’arrivée correspond au comportement par défaut et convient plus particulièrement aux scénarios d’archivage de données, qui ne font appel à aucune logique temporelle.

2. **Heure de l’application** (aussi appelée Heure de l’événement)

   L’heure de l’application est affectée quand l’événement est généré et qu’il fait partie de la charge utile d’événement. Pour traiter les événements selon l’heure de l’application, utilisez la clause **Timestamp by** dans la requête select. En l’absence de la clause **Timestamp by**, les événements sont traités par heure d’arrivée.

   Il est important d’utiliser un horodatage dans la charge utile quand il existe une logique temporelle. De cette façon, les retards dans le système source ou sur le réseau peuvent être pris en compte.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Progression du temps dans Azure Stream Analytics

Quand l’heure de l’application est utilisée, la progression du temps est basée sur les événements entrants. Il est difficile pour le système de traitement de flux de savoir s’il n’existe aucun événement ou si des événements sont retardés. Pour cette raison, Azure Stream Analytics génère des limites heuristiques pour chaque partition d’entrée en procédant comme suit :

1. À chaque fois qu’il y a un événement entrant, la limite est égale à la plus grande durée d’événement observée jusque-là moins la taille de la plage de tolérance pour les événements en désordre.

2. À chaque fois qu’il n’y a pas d’événement entrant, la limite correspond à l’heure d’arrivée estimée actuelle (temps écoulé sur la machine virtuelle qui traite en arrière-plan les événements à partir du moment où un événement d’entrée a été observé pour la dernière fois, auquel s’ajoute l’heure d’arrivée de cet événement d’entrée) moins la plage de tolérance d’arrivée tardive.

   L’heure d’arrivée ne peut être qu’une estimation, car l’heure d’arrivée réelle est générée au niveau du répartiteur d’événements d’entrée, comme Event Hubs, et non sur la machine virtuelle Azure Stream Analytics chargée de traiter les événements.

En plus de la génération de limites, la conception tend vers deux objectifs supplémentaires :

1. Le système génère des résultats en temps voulu avec ou sans événements entrants.

   Vous pouvez contrôler le degré de ponctualité avec lequel les résultats générés doivent s’afficher. Sur le portail Azure, dans la page **Ordre des événements** de votre travail Stream Analytics, vous pouvez configurer le paramètre **Événements dans le désordre**. Au moment de configurer ce paramètre, tachez de trouver le meilleur compromis entre ponctualité et tolérance des événements en désordre dans le flux d’événements.

   La plage de tolérance d’arrivée tardive s’avère importante pour continuer à générer des limites, même en l’absence d’événements entrants. Au cours d’une période donnée, il se peut qu’aucun événement entrant n’arrive, notamment quand un flux d’entrée d’événements est épars. Ce problème est exacerbé quand plusieurs partitions sont utilisées dans le répartiteur d’événements d’entrée.

   Les systèmes de traitement de données de streaming dépourvus d’une plage de tolérance d’arrivée tardive peuvent pâtir de sorties retardées quand les entrées sont éparses et que plusieurs partitions sont utilisées.

2. Le comportement du système doit être répétable. La répétabilité est une propriété importante d’un système de traitement de données de streaming.

   La limite est dérivée de l’heure d’arrivée et de l’heure de l’application. Les deux sont conservées dans le répartiteur d’événements, et sont donc répétables. Dans le cas où l’heure d’arrivée doit être estimée du fait de l’absence d’événements, Azure Stream Analytics journalise l’heure d’arrivée estimée pour la répétabilité pendant la lecture à des fins de récupération après défaillance.

Sachez qu’en choisissant d’utiliser l’**heure d’arrivée** comme heure d’événement, vous n’avez pas besoin de configurer la tolérance de désordre et la tolérance d’arrivée tardive. Comme l’**heure d’arrivée** est vouée à progresser de façon monotone dans le répartiteur d’événements d’entrée, Azure Stream Analytics ignore simplement les configurations.

## <a name="late-arriving-events"></a>Événements tardifs

Conformément à la définition de la plage de tolérance d’arrivée tardive, pour chaque événement entrant, Azure Stream Analytics compare l’**heure de l’événement** à l’**heure d’arrivée** ; si l’heure de l’événement se trouve en dehors de la plage de tolérance, vous pouvez configurer le système de sorte qu’il supprime l’événement ou qu’il ajuste l’heure de l’événement afin qu’elle se trouve dans la plage de tolérance.

Tenez compte du fait qu’une fois les limites générées, le service peut recevoir des événements dont l’heure d’événement se trouve en dessous de la limite. Vous pouvez configurer le service afin qu’il **supprime** ces événements ou qu’il **ajuste** l’heure d’événement en fonction de la valeur limite.

Dans le cadre de l’ajustement, l’élément **System.Timestamp** de l’événement prend la nouvelle valeur, mais le champ **heure de l’événement** proprement dit reste inchangé. Cet ajustement est le seul cas où la valeur **System.Timestamp** d’un événement peut être différente de celle du champ d’heure de l’événement et peut produire des résultats inattendus.

## <a name="handling-time-variation-with-substreams"></a>Gestion des variations d’heure avec les sous-flux

Le mécanisme de génération de limites heuristiques décrit ici fonctionne bien dans la plupart des cas où est l’heure est essentiellement synchronisée entre les différents expéditeurs d’événements. Cependant, dans la réalité, surtout dans bon nombre de scénarios IoT, le système a peu de contrôle sur l’horloge des expéditeurs d’événements. Dans la pratique, ceux-ci peuvent correspondre à toutes sortes d’appareils, peut-être sur différentes versions de matériels et de logiciels.

Au lieu d’utiliser une limite globale pour tous les événements d’une partition d’entrée, Stream Analytics propose un autre mécanisme appelé sous-flux. Vous pouvez utiliser des sous-flux dans votre travail en écrivant une requête de travail qui utilise la clause [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) et le mot clé **OVER**. Pour désigner le sous-flux, indiquez un nom de colonne clé après le mot clé **OVER**, comme `deviceid`, de sorte que le système applique les stratégies de temps en fonction de cette colonne. Chaque sous-flux obtient sa propre limite indépendante. Ce mécanisme est utile pour permettre une génération de sortie en temps voulu, quand il s’agit de faire face à des variations d’horloge importantes ou des retards réseau entre les expéditeurs d’événements.

Les sous-flux sont une solution unique proposée uniquement par Azure Stream Analytics et par aucun autre système de traitement de données de streaming. Stream Analytics applique la plage de tolérance d’arrivée tardive aux événements entrants quand des sous-flux sont utilisés. Le paramétrage par défaut (5 secondes) est probablement trop petit pour les appareils dont les horodatages divergent. Nous vous recommandons de commencer par une valeur de 5 minutes et d’effectuer des ajustements en fonction de la variation des horloges des appareils.

## <a name="early-arriving-events"></a>Événements précoces

Vous avez peut-être observé un autre concept appelé plage d’arrivée précoce, qui est pour ainsi dire l’inverse de la plage de tolérance d’arrivée tardive. Cette plage est fixée à 5 minutes et joue un rôle différent de la plage d’arrivée tardive.

Comme Azure Stream Analytics apporte la garantie de toujours de générer des résultats complets, vous pouvez spécifier uniquement l’**heure de début du travail** comme première heure de sortie du travail, et non l’heure d’entrée. L’heure de début du travail est nécessaire pour permettre le traitement de la plage entière, et pas seulement à partir du milieu de celle-ci.

Stream Analytics dérive alors de l’heure de début de la spécification de la requête. Cependant, sachant que le répartiteur d’événements d’entrée est indexé uniquement par heure d’arrivée, le système doit traduire l’heure de l’événement de début en heure d’arrivée. Le système peut commencer à traiter les événements à partir de cet instant dans le répartiteur d’événements d’entrée. Avec la limite de la plage d’arrivée précoce, la traduction est simple. Il s’agit de l’heure de l’événement de début moins les 5 minutes de la plage d’arrivée précoce. Ce calcul signifie aussi que le système supprime tous les événements dont l’heure présente plus de 5 minutes que l’heure d’arrivée.

Ce concept vise à garantir la répétabilité du traitement, d’où que vous commenciez à effectuer la sortie. Sans un tel mécanisme, il ne serait pas possible de garantir la répétabilité, contrairement à ce que prétendent bien d’autres systèmes de streaming.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Effets secondaires des tolérances d’heures pour l’ordre des événements

Les travaux Stream Analytics proposent plusieurs options **Ordre des événements**. Il est possible d’en configurer deux sur le portail Azure : le paramètre **Événements dans le désordre** (tolérance de désordre) et le paramètre **Événements qui arrivent en retard** (tolérance d’arrivée tardive). La tolérance d’**arrivée précoce** est fixe et ne peut pas être ajustée. Ces stratégies de temps sont utilisées par Stream Analytics pour offrir des garanties solides. Cependant, ces paramètres ont parfois des incidences inattendues :

1. Envoi accidentel d’événements trop précoces.

   Les événements précoces ne devraient normalement pas être générés. Or, il est possible que des événements précoces soient envoyés à la sortie si l’horloge de l’expéditeur avance trop. Tous les événements précoces étant supprimés, vous n’en verrez aucun dans la sortie.

2. Envoi d’événements anciens à Event Hubs pour être traités par Azure Stream Analytics.

   Si les anciens événements peuvent à première vue sembler inoffensifs, l’application de la tolérance d’arrivée tardive peut entraîner leur suppression. Si les événements sont trop anciens, la valeur de **System.Timestamp** est modifiée pendant l’ingestion d’événements. Du fait de ce comportement, Azure Stream Analytics est actuellement plus adapté aux scénarios de traitement d’événements en temps réel, plutôt qu’aux scénarios de traitement d’événements historiques. Dans certains cas, pour contourner ce problème, vous pouvez attribuer au paramètre **Événements qui arrivent en retard** la valeur maximale (20 jours).

3. Les sorties semblent retardées.

   La première limite est générée à l’heure calculée : **heure maximale de l’événement** observée jusque-là par le système moins la taille de la plage de tolérance de désordre. Par défaut, la tolérance de désordre est configurée sur zéro (00 minutes et 00 secondes). Quand vous lui attribuez une valeur supérieure à zéro, la première sortie du travail de streaming est retardée selon cette valeur de temps (ou valeur supérieure) en raison de la première limite de temps calculée.

4. Les entrées sont éparses.

   Quand il n’y a aucune entrée dans une partition donnée, la limite de temps est calculée comme étant la différence entre **heure d’arrivée** et la plage de tolérance d’arrivée tardive. Par conséquent, si les événements d’entrée sont peu fréquents et épars, la sortie peut être retardée de cette durée. La valeur par défaut du paramètre **Événements qui arrivent en retard** est de 5 secondes. Vous devez vous attendre à observer un retard quand vous envoyez les événements d’entrée à tour de rôle, par exemple. Les retards peuvent s’accentuer si vous attribuez une valeur élevée au paramètre **Événements qui arrivent en retard**.

5. La valeur de **System.Timestamp** est différente de l’heure indiquée dans le champ **Heure de l’événement**.

   Comme indiqué précédemment, le système ajuste l’heure d’événement en fonction des plages de tolérance de désordre ou d’arrivée tardive. La valeur de **System.Timestamp** de l’événement est ajustée, mais pas celle du champ **heure de l’événement**.

## <a name="metrics-to-observe"></a>Métriques à observer

Vous pouvez observer un certain nombre d’effets liés à la tolérance de temps Ordre des événements via les [métrique de travail Stream Analytics](stream-analytics-monitoring.md). Les métriques à prendre en considération sont les suivantes :

|Métrique  | Description  |
|---------|---------|
| **Événements dans le désordre** | Indique le nombre d’événements reçus dans le désordre, qui ont été supprimés ou dont l’horodatage a été ajusté. Cette mesure est directement impactée par la configuration du paramètre **Événements dans le désordre** de la page **Ordre des événements** du travail sur le portail Azure. |
| **Événements d’entrée tardifs** | Indique le nombre d’événements qui arrivent tardivement de la source. Cette métrique englobe les événements qui ont été supprimés ou dont l’horodatage a été ajusté. Elle est directement impactée par la configuration du paramètre **Événements qui arrivent en retard** de la page **Ordre des événements** du travail sur le portail Azure. |
| **Événements d’entrée précoces** | Indique le nombre d’événements arrivant précocement de la source, qui ont été supprimés ou dont l’horodatage a été ajusté si leur précocité était supérieure à 5 minutes. |
| **Délai en filigrane** | Indique le retard du travail de traitement de données de streaming. Vous trouverez des informations complémentaires dans la section suivante.|

## <a name="watermark-delay-details"></a>Détails de la métrique Délai en filigrane

La métrique **Délai en filigrane** est calculée comme étant le temps horloge du nœud de traitement moins la limite supérieure observée jusqu’à présent. Pour plus d’informations, consultez le [billet de blog relatif au délai en filigrane](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Plusieurs raisons peuvent expliquer le fait que cette valeur de métrique soit supérieure à 0 dans des conditions normales de fonctionnement :

1. Retard de traitement inhérent au pipeline de streaming. Normalement, ce délai est minime.

2. La plage de tolérance de désordre a occasionné un retard, car la limite est réduite de la taille de la plage de tolérance.

3. La plage d’arrivée tardive a occasionné un retard, car la limite est réduite de la taille de la plage de tolérance.

4. Variation d’horloge du nœud de traitement générant la métrique.

Il existe un certain nombre d’autres contraintes de ressource qui peuvent occasionner un ralentissement au niveau du pipeline de streaming. Les facteurs qui peuvent faire augmenter la valeur de la métrique de délai en filigrane sont les suivants :

1. Insuffisance des ressources de traitement Stream Analytics pour traiter le volume d’événements d’entrée. Pour procéder au scale-up des ressources, consultez [Comprendre et ajuster les unités de streaming](stream-analytics-streaming-unit-consumption.md).

2. Limitation des répartiteurs d’événements d’entrée du fait d’un débit insuffisant. Pour voir les solutions possibles, consultez [Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Limitation des récepteurs de sortie du fait d’une capacité provisionnée insuffisante. Les solutions possibles varient grandement selon la version du service de sortie utilisé.

## <a name="output-event-frequency"></a>Fréquence des événements de sortie

Azure Stream Analytics se sert de la progression de la limite comme unique déclencheur de la génération des événements de sortie. Sachant que la limite est dérivée des données d’entrée, elle est répétable pendant la récupération après une défaillance, mais aussi dans le retraitement initié par l’utilisateur.

En cas d’utilisation des [agrégations fenêtrées](stream-analytics-window-functions.md), le service génère uniquement les sorties à la fin des plages. Dans certains cas, les utilisateurs peuvent souhaiter voir les agrégations partielles générées à partir des plages. Les agrégations partielles ne sont pas prises en charge dans Azure Stream Analytics.

Dans d’autres solutions de streaming, les événements de sortie peuvent être matérialisés à divers points de déclenchement, en fonction de circonstances extérieures. Dans certaines solutions, il est possible que les événements de sortie pour une plage de temps donnée soient générés plusieurs fois. Les résultats d’agrégation se font plus précis à mesure que les valeurs d’entrée sont affinées. Dans un premier temps, les événements peuvent être extrapolés et revus au fil du temps. Par exemple, quand un appareil est hors connexion sur le réseau, un système peut utiliser une valeur estimée. Par la suite, quand ce même appareil est connecté au réseau, les données d’événements réelles peuvent être incluses dans le flux d’entrée. Les résultats générés par le traitement de cette plage de temps offrent une sortie plus précise.

## <a name="illustrated-example-of-watermarks"></a>Exemple illustrés de limites

Les images suivantes illustrent la façon dont les limites progressent dans certaines circonstances.

Ce tableau montre les données de l’exemple représentées sous forme de graphique ci-dessous. Notez que l’heure de l’événement et l’heure d’arrivée peuvent parfois correspondre, parfois pas.

| Heure de l’événement | Heure d’arrivée | deviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Dans cette illustration, les tolérances utilisées sont les suivantes :

- La plage d’arrivée précoce est de 5 minutes
- La plage d’arrivée tardive est de 5 minutes
- La plage de réorganisation est de 2 minutes

1. Illustration de la progression de la limite à travers ces événements :

   ![Illustration de la limite Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processus notables illustrés dans le graphique précédent :

   1. Le premier événement (device1) et le deuxième événement (device2) ont des heures qui concordent et sont traités sans ajustement. La limite progresse à chaque événement.

   2. Quand le troisième événement (device1) est traité, l’heure d’arrivée (12:11) est antérieure à l’heure de l’événement (12:17). L’événement étant arrivé avec 6 minutes d’avance, l’événement est supprimé du fait de la tolérance d’arrivée précoce de 5 minutes.

      La limite ne progresse pas ici dans le cas d’un événement précoce.

   3. Le quatrième événement (device3) et le cinquième événement (device1) ont des heures qui concordent et sont traités sans ajustement. La limite progresse à chaque événement.

   4. Quand le sixième événement (device3) est traité, l’heure d’arrivée (12:17) et l’heure de l’événement (12:12) se trouvent en dessous du niveau limite. L’heure de l’événement est ajustée par rapport au niveau limite (12:17).

   5. Quand le douzième événement (device3) est traité, l’heure d’arrivée (12:27) est postérieure de 6 minutes à l’heure de l’événement (12:21). La stratégie d’arrivée tardive est appliquée. L’heure de l’événement est ajustée (12:22), laquelle se situe au-dessus de la limite (12:21), et aucun ajustement supplémentaire n’est appliqué.

2. Deuxième illustration de la progression de la limite sans stratégie d’arrivée précoce :

   ![Illustration de la limite sans stratégie précoce Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-2.png)

   Dans cet exemple, aucune stratégie d’arrivée précoce n’est appliquée. Les événements qui arrivent précocement et qui ont une valeur hors norme relèvent considérablement la limite. À noter que le troisième événement (deviceId1 à 12:11) n’est pas supprimé dans ce scénario et que la limite monte à 12:15. De ce fait, l’heure du quatrième événement est ajustée de 7 minutes (de 12:08 à 12:15).

3. Dans la dernière illustration, des sous-flux sont utilisés (OVER DeviceID). Plusieurs limites font l’objet d’un suivi, un par flux. De ce fait, les événements dont l’heure est ajustée sont moins nombreux.

   ![Illustration de la limite de sous-flux Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Étapes suivantes

- [Considérations relatives à l’ordre des événements Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Métriques de travail Stream Analytics](stream-analytics-monitoring.md)
