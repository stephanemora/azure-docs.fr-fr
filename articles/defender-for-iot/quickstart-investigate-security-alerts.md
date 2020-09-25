---
title: 'Démarrage rapide : Investiguer les alertes de sécurité'
description: Comprenez, explorez et investiguez les alertes de sécurité Defender pour IoT sur vos appareils IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944138"
---
# <a name="quickstart-investigate-security-alerts"></a>Démarrage rapide : Investiguer les alertes de sécurité

Une investigation et une correction planifiées des alertes émises par Defender pour IoT constituent la meilleure façon de garantir la conformité et la protection dans votre solution IoT.

Dans ce démarrage rapide, nous allons explorer les informations disponibles dans chaque alerte de sécurité IoT et expliquer comment explorer et utiliser les détails de chaque alerte et appareil associé, pour réagir et corriger. 

C’est parti ! 


## <a name="investigate-new-security-alerts"></a>Investiguer de nouvelles alertes de sécurité

La liste des alertes de sécurité IoT Hub affiche toutes les alertes de sécurité agrégées pour votre IoT Hub. 

1. Dans le portail Azure, ouvrez l’**IoT Hub** à investiguer pour de nouvelles alertes.
1. Dans le menu **Sécurité**, sélectionnez **Alertes**. Toutes les alertes de sécurité pour l’IoT Hub s’affichent. Les alertes avec un indicateur **Nouvelle** marquent les alertes des dernières 24 heures.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Investiguer les nouvelles alertes de sécurité IoT à l’aide de l’indicateur de nouvelle alerte":::
1. Sélectionnez et ouvrez une alerte dans la liste pour ouvrir ses détails et en explorer les spécificités. 

## <a name="security-alert-details"></a>Détails des alertes de sécurité

L’ouverture de chaque alerte agrégée affiche la description détaillée de celle-ci, les étapes de correction, l’ID de chaque appareil ayant déclenché une alerte, ainsi que la gravité de l’alerte et un accès direct à l’investigation à l’aide de Log Analytics. 

1. Sélectionnez et ouvrez une alerte de sécurité à partir de la liste **IoT Hub** > **Sécurité** > **Alertes**. 
1. Examinez la **description**, la **gravité**, la **source de la détection** et les **détails de l’appareil** en lien avec l’alerte de tous les appareils qui ont émis celle-ci au cours de la période d’agrégation.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Explorer et examiner les détails de chaque appareil dans une alerte agrégée"::: 
1. Après avoir vérifié les spécificités de l’alerte, suivez les instructions de l’**étape de correction manuelle** afin d’atténuer ou résoudre le problème à l’origine de l’alerte. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Suivre les étapes de correction manuelle pour vous atténuer ou résoudre les alertes de sécurité de votre appareil":::

1. Si une investigation supplémentaire est nécessaire, **examinez les alertes dans Log Analytics** en suivant le lien. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Pour investiguer davantage sur une alerte, utiliser le lien pour examiner à l’aide de Log Analytics affiché à l’écran":::

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour en savoir plus sur les types d’alerte Defender et les personnalisations possibles.

> [!div class="nextstepaction"]
> [Compréhension des alertes de sécurité IoT](concept-security-alerts.md)
