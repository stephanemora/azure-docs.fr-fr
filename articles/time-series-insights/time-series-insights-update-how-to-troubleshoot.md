---
title: Diagnostiquer et résoudre les problèmes d’un environnement en préversion - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment diagnostiquer et résoudre les problèmes d’un environnement Azure Time Series Insights en préversion.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.openlocfilehash: 0e4ec63ffe715b17f55fde2a53c15d96d391cdba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452502"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnostiquer et dépanner un environnement d’évaluation

Cet article résume plusieurs problèmes courants que vous pourriez rencontrer lorsque vous travaillez avec votre environnement Azure Time Series Insights en préversion. L’article décrit également les causes et solutions potentielles de chaque problème.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problème : Mon environnement est introuvable dans l’explorateur en préversion

Ce problème peut se produire si vous n’êtes pas autorisé à accéder à l’environnement Time Series Insights. Les utilisateurs ont besoin d’un rôle d’accès de niveau lecteur pour visualiser leur environnement Time Series Insights. Pour vérifier les niveaux d’accès actuels et accorder un accès supplémentaire, accédez à la section **Stratégies d’accès aux données** sur la ressource Insights Time Series du [Portail Azure](https://portal.azure.com/).

  [![Vérifiez les stratégies d’accès aux données.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problème : aucune donnée n’apparaît dans l’explorateur en préversion

Il existe plusieurs raisons pour lesquelles vous ne pouvez pas voir vos données dans [l’explorateur Azure Time Series Insights en préversion](https://insights.timeseries.azure.com/preview).

- Votre source d’événement ne reçoit peut-être pas de données.

    Vérifiez que votre source d’événement, qui est un event Hub ou un hub IoT, reçoit des données de vos balises ou instances. Pour ce faire, rendez-vous sur la page d’aperçu de votre ressource dans le portail Azure.

    [![Vue d’ensemble des métriques du tableau de bord des évaluations.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Vos données sources d’événements ne sont pas au format JSON.

    Time Series Insights ne prend en charge que les données JSON. Pour obtenir des exemples de données JSON, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).

- Il manque une autorisation requise pour votre clé de source d’événements.

  * Pour IoTHub, vous devez fournir la clé avec l’autorisation **Connexion de service**.

    [![Vérifiez les autorisations IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Les stratégies **iothubowner** et **service** fonctionnent toutes les deux car elles disposent de l’autorisation **Connexion de service**.

  * Pour Event Hub, vous devez fournir la clé avec l’autorisation **Écouter**.
  
    [![Passez en revue les autorisations du hub d’événements.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Les stratégies **Read** et **Manage** fonctionnent toutes les deux car elles disposent de l’autorisation **Écouter**.

- Votre groupe de consommateurs fourni n’est pas exclusif à Time Series Insights.

    Lors de l’enregistrement d’un IoT Hub ou Event Hub, spécifiez le groupe de consommateurs utilisé pour la lecture des données. Ce groupe de consommateurs doit être unique par environnement. Si le groupe de consommateurs est partagé, le concentrateur d’événements sous-jacent déconnecte automatiquement l’un des lecteurs au hasard. Fournissez un groupe de consommateurs unique auprès duquel Time Series Insights lira les informations.

- Votre propriété ID Time Series spécifiée au moment de l’approvisionnement est incorrecte, manquante ou nulle.

    Ce problème peut survenir si la propriété Time Series ID (ID de série chronologique) est mal configurée au moment de l’approvisionnement de l’environnement. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md). Pour le moment, vous ne pouvez pas mettre à jour un environnement Time Series Insights existant pour utiliser un ID de série chronologique différent.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problème : Certaines données s’affichent, mais d’autres manquent

Il se peut que vous envoyiez des données sans l’ID de série chronologique.

- Ce problème peut survenir lorsque vous envoyez des événements sans le champ ID de série chronologique dans la charge utile. Pour plus d’informations, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).
- Ce problème peut survenir parce que votre environnement est limité.

    > [!NOTE]
    > À l’heure actuelle, Time Series Insights prend en charge un taux d’ingestion maximal de 6 Mbit/s.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problème : le nom de la propriété Timestamp de ma source d’événement ne fonctionne pas

Vérifiez que le nom et la valeur répondent aux critères suivants :

* Le nom de la propriété Timestamp est sensible à la casse.
* La valeur de la propriété Timestamp provenant de votre source d’événement, telle qu’une chaîne JSON, est au format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Exemple de chaîne : `“2008-04-12T12:53Z”`.

La méthode la plus simple pour vous assurer que votre nom de propriété Timestamp est capturé et fonctionne correctement consiste à utiliser l’explorateur Time Series Insights en préversion. Dans l’explorateur Time Series Insights en préversion, utilisez le graphique pour sélectionner une période de temps après avoir indiqué le nom de la propriété Timestamp. Cliquez avec le bouton droit sur la sélection et sélectionnez l’option **Explorer les événements**. Le premier en-tête de colonne correspond au nom de votre propriété Timestamp. Il devrait contenir `($ts)` en regard du mot `Timestamp` plutôt que :

* `(abc)`, ce qui indique que Time Series Insights lit les valeurs de données sous forme de chaînes.
* L’**icône de calendrier**, qui indique que Time Series Insights lit la valeur de données sous forme de date/heure.
* `#`, ce qui indique que Time Series Insights lit les valeurs de données sous forme d’entier.

Si la propriété Timestamp n’est pas explicitement spécifiée, l’horodatage par défaut est l’heure de mise en file d’attente du hub IoT ou du hub Event.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problème : je ne peux pas afficher les données de mon magasin Warm dans l’Explorateur

- Vous avez peut-être approvisionné votre magasin Warm récemment et les données sont toujours en cours d’acheminement.
- Vous avez peut-être supprimé votre magasin Warm, auquel cas vous risquez de perdre des données.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problème : je n’arrive pas à afficher ou à modifier mon modèle de série chronologique

- Vous accédez peut-être à un environnement Time Series Insights S1 ou S2.

   Les modèles de séries chronologiques ne sont pris en charge que dans les environnements de paiement à l’utilisation. Pour plus d’informations sur la façon d’accéder à votre environnement S1 ou S2 à partir de l’explorateur Time Series Insights en préversion, consultez [Visualiser les données dans l’Explorateur](./time-series-insights-update-explorer.md).

   [![Aucun événement dans l’environnement.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Il se peut que vous n’ayez pas les autorisations nécessaires pour afficher et modifier le modèle.

   Les utilisateurs ont besoin d’un accès de niveau contributeur pour modifier et visualiser leur modèle de série chronologique. Pour vérifier les niveaux d’accès actuels et accorder un accès supplémentaire, accédez à la section **Stratégies d’accès aux données** sur votre ressource Insights Time Series du Portail Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problème : toutes mes instances dans l’Explorateur en préversion n’ont pas de parent

Ce problème peut se produire si votre environnement n’a pas de hiérarchie Time Series Model définie. Pour plus d’informations, consultez [Utiliser des modèles de série chronologique](./time-series-insights-update-how-to-tsm.md).

  [![Les instances non apparentées affichent un avertissement.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Utiliser des modèles de série chronologique](./time-series-insights-update-how-to-tsm.md).

- En savoir plus sur les [formes JSON prises en charge](./how-to-shape-query-json.md).

- Passez en revue [la planification et les limites](./time-series-insights-update-plan.md) dans Azure Time Series Insights version préliminaire.
