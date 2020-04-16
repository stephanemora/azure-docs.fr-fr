---
title: Agrégation des événements
description: Apprenez-en davantage sur l’agrégation d’événements Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311418"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Agrégation d’événements Azure Security Center pour IoT

Les agents de sécurité Azure Security Center pour IoT recueillent des données et des événements système à partir de votre appareil local et envoient ces données au cloud Azure à des fins de traitement et d’analytique. L’agent de sécurité collecte de nombreux types d’événements d’appareil, notamment des événements relatifs aux nouveaux processus et aux nouvelles connexions. Des événements relatifs aux nouveaux processus et aux nouvelles connexions peuvent se produire fréquemment sur un appareil en une seconde, et ce, de façon légitime. Même si cette collecte d’événements est essentielle pour une sécurité robuste et complète, le nombre de messages que les agents de sécurité sont obligés d’envoyer peut rapidement atteindre ou dépasser vos limites de coût et votre quota IoT Hub. Toutefois, ces événements contiennent des informations de sécurité très précieuses qui sont essentielles à la protection de votre appareil.

Pour réduire le quota et les coûts supplémentaires tout en conservant la protection de vos appareils, les agents Azure Security Center pour IoT agrègent ces types d’événements.

L’agrégation d’événements est **activée** par défaut et, bien que cela ne soit pas recommandé, elle peut être **désactivée** manuellement à tout moment.

L’agrégation est actuellement disponible pour les types d’événements suivants :

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (Windows uniquement)

## <a name="how-does-event-aggregation-work"></a>Fonctionnement de l’agrégation d’événements

Lorsque l’agrégation d’événements est **activée**, les agents Azure Security Center pour IoT agrègent les événements pour la fenêtre de temps ou la période d’intervalle.
Une fois la période d’intervalle écoulée, l’agent envoie les événements agrégés au cloud Azure pour une analyse plus poussée.
Les événements agrégés sont stockés en mémoire jusqu’à ce qu’ils soient envoyés au cloud Azure.

Pour réduire l’empreinte mémoire de l’agent, chaque fois que l’agent collecte un événement identique à celui qui est déjà conservé en mémoire, l’agent augmente le nombre d’accès de cet événement spécifique. Lorsque la fenêtre de temps d’agrégation se termine, l’agent envoie le nombre d’accès de chaque type spécifique d’événement qui s’est produit. L’agrégation d’événements est simplement l’agrégation des nombres d’accès de chaque type collecté d’événement.

Les événements sont considérés comme identiques uniquement lorsque les conditions suivantes sont remplies :

* Événements ProcessCreate : quand **commandLine**, **executable**, **username** et **userid** sont identiques
* Événements ConnectionCreate : quand **commandLine**, **userId**, **direction**, **local address**, **remote address**, **protocol et **destination port** sont identiques
* Événements ProcessTerminate : quand **executable** et **exit status** sont identiques

### <a name="working-with-aggregated-events"></a>Utilisation des événements agrégés

Pendant l’agrégation, les propriétés d’événement qui ne sont pas agrégées sont ignorées et apparaissent dans Log Analytics avec la valeur 0.

* Événements ProcessCreate : **ProcessId** et **parentProcessId** sont définis sur 0
* Événements ConnectionCreate : **ProcessId** et **source port** sont définis sur 0

## <a name="event-aggregation-based-alerts"></a>Alertes basées sur une agrégation d’événements

Après analyse, Azure Security Center pour IoT crée des alertes de sécurité pour les événements agrégés suspects. Les alertes créées à partir d’événements agrégés apparaissent une seule fois pour chaque événement agrégé.

L’heure de début, l’heure de fin et le nombre d’accès de l’agrégation pour chaque événement sont consignés dans le champ **ExtraDetails** de l’événement dans Log Analytics pour une utilisation lors des investigations.

Chaque événement agrégé représente une période de 24 heures des alertes collectées. Grâce au menu d’options d’événement en haut à gauche de chaque événement, vous pouvez **masquer** chaque événement agrégé individuel.

## <a name="event-aggregation-twin-configuration"></a>Configuration du jumeau d’agrégration d’événements

Apportez des modifications à la configuration de l’agrégration d’événements Azure Security Center pour IoT à l’intérieur de l’[objet de configuration de l’agent](how-to-agent-configuration.md) de l’identité de jumeau de module du module **azureiotsecurity**.

| Nom de la configuration | Valeurs possibles | Détails | Notes |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Activer/désactiver l’agrégation d’événements pour les événements de création de processus |
| aggregationIntervalProcessCreate | Chaîne TimeSpan ISO8601 | Intervalle d’agrégation pour les événements de création de processus |
| aggregationEnabledConnectionCreate | boolean| Activer/désactiver l’agrégation d’événements pour les événements de création de connexion |
| aggregationIntervalConnectionCreate | Chaîne TimeSpan ISO8601 | Intervalle d’agrégation pour les événements de création de connexion |
| aggregationEnabledProcessTerminate | boolean | Activer/désactiver l’agrégation d’événements pour les événements de fin de processus | Windows uniquement|
| aggregationIntervalProcessTerminate | Chaîne TimeSpan ISO8601 | Intervalle d’agrégation pour les événements de fin de processus | Windows uniquement|
|

## <a name="default-configurations-settings"></a>Paramètres de configuration par défaut

| Nom de la configuration | Valeurs par défaut |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’agréation de l’agent de sécurité Azure Security Center pour IoT, ainsi que les options de configuration d’événement disponibles.

Pour continuer votre prise en main du déploiement d’Azure Security Center pour IoT, lisez les articles suivants :

- Comprendre les [méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- Sélectionner et déployer un [agent de sécurité](how-to-deploy-agent.md)
- Réviser les [composants requis pour le service](service-prerequisites.md) Azure Security Center pour IoT
- Découvrez comment [activer le service Azure Security Center pour IoT dans votre hub IoT](quickstart-onboard-iot-hub.md).
- Trouvez des informations sur le service dans la [FAQ Azure Security Center pour IoT](resources-frequently-asked-questions.md)
