---
title: Diagnostiquer et résoudre les problèmes d’Azure Time Series Insights en préversion | Microsoft Docs
description: Découvrez comment diagnostiquer et résoudre les problèmes d’Azure Time Series Insights en préversion.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab3c680f7279ff78e0319f28f67c1cc8c203b47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708021"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnostiquer et résoudre les problèmes

Cet article résume plusieurs problèmes courants que vous pourriez rencontrer lorsque vous travaillez avec votre environnement Azure Time Series Insights en préversion. L’article décrit également les causes et solutions potentielles de chaque problème.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problème : Mon environnement est introuvable dans l’explorateur Time Series Insights en préversion

Ce problème peut se produire si vous n’êtes pas autorisé à accéder à l’environnement Time Series Insights. Les utilisateurs ont besoin d’un rôle d’accès de niveau lecteur pour visualiser leur environnement Time Series Insights. Pour vérifier les niveaux d’accès actuels et accorder un accès supplémentaire, consultez la section Stratégies d’accès aux données sur la ressource Insights Time Series du [portail Azure](https://portal.azure.com/).

  ![Environnement][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problème : Aucune donnée n’est affichée dans l’explorateur Time Series Insights en préversion

Il existe plusieurs raisons pour lesquelles vous ne pouvez pas voir vos données dans [l’explorateur Azure Time Series Insights en préversion](https://insights.timeseries.azure.com/preview).

- Votre source d’événement ne reçoit peut-être pas de données.

    Vérifiez que votre source d’événement, qui est un event Hub ou un hub IoT, reçoit des données de vos balises ou instances. Pour ce faire, rendez-vous sur la page d’aperçu de votre ressource dans le portail Azure.

    ![Dashboard-insights][2]

- Vos données sources d’événements ne sont pas au format JSON.

    Time Series Insights ne prend en charge que les données JSON. Pour obtenir des exemples de données JSON, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).

- Il manque une autorisation requise pour votre clé de source d’événements.

  * Pour IoTHub, vous devez fournir la clé avec l’autorisation **Connexion de service**.

    ![Configuration][3]

  * Comme indiqué dans l’illustration précédente, les stratégies **iothubowner** et **service** sont acceptées, car elles disposent de l’autorisation **Connexion de service**.
  * Pour Event Hub, vous devez fournir la clé avec l’autorisation **Écouter**.
  
    ![Autorisations][4]

  * Comme indiqué dans l’illustration précédente, les stratégies **read** et **manage** sont acceptées, car elles disposent de l’autorisation **Listen**.

- Votre groupe de consommateurs fourni n’est pas exclusif à Time Series Insights.

    Lors de l’enregistrement d’un IoT Hub ou Event Hub, spécifiez le groupe de consommateurs utilisé pour la lecture des données. Ne partagez pas ce groupe de consommateurs. Si le groupe de consommateurs est partagé, le concentrateur d’événements sous-jacent déconnecte automatiquement l’un des lecteurs au hasard. Fournissez un groupe de consommateurs unique auprès duquel Time Series Insights lira les informations.

- Votre propriété ID Time Series spécifiée au moment de l’approvisionnement est incorrecte, manquante ou nulle.

    Ce problème peut survenir si la propriété Time Series ID (ID de série chronologique) est mal configurée au moment de l’approvisionnement de l’environnement. Pour plus d’informations, consultez [Meilleures pratiques pour le choix d’un ID Time Series](./time-series-insights-update-how-to-id.md). Pour le moment, vous ne pouvez pas mettre à jour un environnement Time Series Insights existant pour utiliser un ID de série chronologique différent.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problème : Certaines données s’affichent, mais d’autres manquent

Il se peut que vous envoyiez des données sans l’ID de série chronologique.

- Ce problème peut survenir lorsque vous envoyez des événements sans le champ ID de série chronologique dans la charge utile. Pour plus d’informations, consultez [Structures JSON prises en charge](./how-to-shape-query-json.md).

- Ce problème peut survenir parce que votre environnement est limité.

    > [!NOTE]
    > À l’heure actuelle, Time Series Insights prend en charge un taux d’ingestion maximal de 6 Mbit/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problème : Le paramètre Nom de la propriété timestamp de ma source d’événement ne fonctionne pas

Vérifiez que le nom et la valeur répondent aux critères suivants :

* Le nom de la propriété Timestamp est sensible à la casse.
* La valeur de la propriété Timestamp provenant de votre source d’événement, telle qu’une chaîne JSON, est au format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Exemple de chaîne : `“2008-04-12T12:53Z”`.

La méthode la plus simple pour vous assurer que votre nom de propriété Timestamp est capturé et fonctionne correctement consiste à utiliser l’explorateur Time Series Insights en préversion. Dans l’explorateur Time Series Insights en préversion, utilisez le graphique pour sélectionner une période de temps après avoir indiqué le nom de la propriété Timestamp. Cliquez avec le bouton droit sur la sélection et sélectionnez l’option **Explorer les événements**. Le premier en-tête de colonne correspond au nom de votre propriété Timestamp. Il devrait contenir `($ts)` en regard du mot `Timestamp` plutôt que :

* `(abc)`, ce qui indique que Time Series Insights lit les valeurs de données sous forme de chaînes.
* Icône de calendrier, ce qui indique que Time Series Insights lit la valeur de données sous forme de date/heure.
* `#`, ce qui indique que Time Series Insights lit les valeurs de données sous forme d’entier.

Si la propriété Timestamp n’est pas explicitement spécifiée, l’horodatage par défaut est l’heure de mise en file d’attente du hub IoT ou du hub Event.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problème : Je n’arrive pas à modifier ou afficher mon modèle de série chronologique

- Vous accédez peut-être à un environnement Time Series Insights S1 ou S2.

   Les modèles de séries chronologiques ne sont pris en charge que dans les environnements de paiement à l’utilisation. Pour plus d’informations sur la façon d’accéder à votre environnement S1/S2 à partir de l’explorateur Time Series Insights en préversion, consultez [Visualiser les données dans l’Explorateur](./time-series-insights-update-explorer.md).

   ![Access][5]

- Il se peut que vous n’ayez pas les autorisations nécessaires pour afficher et modifier le modèle.

   Les utilisateurs ont besoin d’un accès de niveau contributeur pour modifier et visualiser leur modèle de série chronologique. Pour vérifier les niveaux d’accès actuels et accorder un accès supplémentaire, consultez la section Stratégies d’accès aux données sur votre ressource Insights Time Series du portail Azure.

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problème : Certaines de mes instances dans l’explorateur Time Series Insights en préversion n’ont pas de parent

Ce problème peut se produire si votre environnement n’a pas de hiérarchie Time Series Model définie. Pour plus d’informations, consultez [Utiliser des modèles de série chronologique](./time-series-insights-update-how-to-tsm.md).

  ![Modèles de série chronologique][6]

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Utiliser des modèles de série chronologique](./time-series-insights-update-how-to-tsm.md).

- En savoir plus sur [pris en charge de structures JSON](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png