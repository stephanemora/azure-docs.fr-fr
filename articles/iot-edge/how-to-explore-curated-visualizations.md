---
title: Explorer les visualisations organisées – Azure IoT Edge
description: Utiliser les workbooks Azure pour visualiser et explorer les métriques intégrées IoT Edge
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0ca0d1fb7890f1a1a94419f58587f3a98957f41c
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904420"
---
# <a name="explore-curated-visualizations-preview"></a>Explorer les visualisations organisées (Préversion)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Vous pouvez visualiser et explorer les métriques collectées depuis l’appareil IoT Edge, directement dans le portail Azure à l’aide des workbooks Azure Monitor. Les workbooks de supervision organisés pour les appareils IoT Edge sont fournis sous forme de modèles publics auxquels vous pouvez accéder à partir du panneau **IoT Hub** de la page **Workbooks** (sous la section Supervision).

Les workbooks organisés utilisent des [métriques intégrées](how-to-access-built-in-metrics.md) provenant du runtime IoT Edge. Ces vues n’ont pas besoin d’instrumentation de métriques à partir des modules de charge de travail.

## <a name="access-curated-workbooks"></a>Accéder aux workbooks organisés

Les workbooks Azure Monitor pour IoT constituent un ensemble de modèles dont vous pouvez vous servir pour démarrer immédiatement la visualisation de vos métriques, ou que vous pouvez personnaliser en fonction de votre solution.

Pour accéder aux workbooks organisés, suivez ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Sélectionnez **Workbooks** dans la section **Supervision** du menu.

1. Choisissez le workbook que vous souhaitez explorer dans la liste des modèles publics :

   * **Vue de la flotte IoT Edge** : supervisez votre flotte d’appareils et explorez des appareils particuliers pour un instantané d’intégrité.
   * **Détails d’un appareil IoT Edge** : visualisez les détails d’un appareil au niveau de la messagerie, des modules et des composants hôtes sur un appareil IoT Edge.
   * **Instantané d’intégrité IoT Edge** : affichez l’intégrité d’un appareil basée sur six métriques de performances courantes. Pour accéder au workbook d’instantané d’intégrité, démarrez dans le workbook des vues de la flotte et sélectionnez l’appareil particulier que vous souhaitez consulter. Le workbook des vues de la flotte transmet certains paramètres nécessaires à la vue d’instantané d’intégrité.

Vous pouvez explorer les workbooks par vous-même ou utiliser les sections suivantes pour obtenir un aperçu du type de données et des visualisations qu’offre chaque workbook.

## <a name="iot-edge-fleet-view-workbook"></a>Workbook des vues de la flotte IoT Edge

Le workbook des vues de la flotte comporte deux vues que vous pouvez utiliser :

* La vue **Appareils** offre une vue d’ensemble des appareils actifs.
* La vue **Alertes** renseignent sur les alertes générées à partir de [règles d’alerte préconfigurées](how-to-create-alerts.md).

Vous pouvez basculer entre les vues à l’aide des onglets situés en haut du workbook.

# <a name="devices"></a>[Appareils](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="Section Appareils du workbook des vues de la flotte." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

Consultez la vue d’ensemble des appareils actifs qui envoient des métriques dans la vue **Appareils**. Cette vue montre les appareils associés à l’instance IoT Hub actuel.

À droite, la liste des appareils contient des barres composites qui indiquent les messages locaux et en amont envoyés. Vous pouvez filtrer la liste par nom d’appareil et cliquer sur le lien du nom de l’appareil pour afficher ses métriques détaillées.

À gauche, la visualisation des cellules de la ruche renseigne sur les appareils sains ou non sains. Elle informe également sur la date de la dernière transmission des métriques effectuée par l’appareil. Les appareils qui n’ont pas envoyé de métriques depuis plus de 30 minutes sont affichés en bleu. Cliquez sur le nom de l’appareil dans la cellule de la ruche pour afficher son instantané d’intégrité. Seules les 3 dernières mesures de l’appareil sont retenues lors de la détermination de l’intégrité. L’utilisation uniquement de données récentes rend compte des pics temporaires dans les métriques signalées.

# <a name="alerts"></a>[Alertes](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="Section des alertes dans le workbook des vues de la flotte." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

Consultez les alertes générées à partir des [règles d’alerte créées au préalable](how-to-create-alerts.md) dans la vue **Alertes**. Cette vue vous permet d’afficher les alertes provenant de plusieurs hubs IoT.

À gauche, la liste des niveaux de gravité des alertes, avec leur nombre d’alertes. À droite, une carte indiquant le nombre total d’alertes par région.

Cliquez sur un niveau de gravité pour afficher les détails des alertes. Le lien **Règle d’alerte** vous permet d’accéder au contexte de l’alerte, et le lien **Appareil** ouvre le workbook de métriques détaillées. Lorsqu’il est ouvert à partir de cette vue, le workbook des détails de l’appareil est automatiquement réglé sur l’intervalle de temps pendant lequel l’alerte s’est déclenchée.

---

## <a name="iot-edge-device-details-workbook"></a>Workbook des détails de l’appareil IoT Edge

Le workbook des détails de l’appareil comporte trois vues que vous pouvez utiliser :

* La vue **Messagerie** visualise les routes des messages pour l’appareil et renseigne sur l’intégrité globale du système de messagerie.
* La vue **Modules** montre le fonctionnement des différents modules d’un appareil.
* La vue **Hôte** présente des informations sur l’appareil hôte, entre autres des informations de version sur les composants hôtes et l’utilisation des ressources.

Vous pouvez basculer entre les vues à l’aide des onglets situés en haut du workbook.

Le workbook des détails de l’appareil s’intègre également à l’expérience de résolution des problèmes figurant sur le portail IoT Edge, afin que vous puissiez afficher les **Journaux dynamiques** provenant de votre appareil. Vous pouvez accéder à cette expérience en sélectionnant le bouton **Résoudre les problèmes de \<device name> en temps réel** au-dessus du workbook.

# <a name="messaging"></a>[Messagerie](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="Section Messagerie du workbook des détails de l’appareil." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

La vue **Messagerie** comprend trois sous-sections : les détails du routage, un graphe du routage et l’intégrité de la messagerie. Effectuez un glisser-déposer sur un graphe de temps pour ajuster l’intervalle de temps global à la plage sélectionnée.

La section **Routage** affiche le flux des messages entre les modules d’envoi et les modules de réception. Elle présente des informations, telles que le nombre de messages, le débit et le nombre de clients connectés. Cliquez sur un expéditeur ou un destinataire pour approfondir l’exploration. En cliquant sur un expéditeur, vous affichez le graphique des tendances de la latence expérimentée par l’expéditeur, et le nombre de messages qu’il a envoyés. Le fait de cliquer sur un destinataire renseigne sur les tendances de la longueur de la file d’attente pour le destinataire, et le nombre de messages qu’il reçoit.

La section **Graphe** montre une représentation visuelle du flux des messages circulant entre les modules. Vous pouvez effectuer un glisser-zoomer pour ajuster le graphe.

La section **Intégrité** présente différentes métriques liées à l’intégrité globale du sous-système de messagerie. Vous pouvez approfondir progressivement les détails si des erreurs sont signalées.

# <a name="modules"></a>[Modules](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="Section Modules du workbook des détails de l’appareil." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

La vue **Modules** présente les métriques collectées du module edgeAgent, qui indique l’état de tous les modules en cours d’exécution sur l’appareil. Il comporte des informations telles que :

* Disponibilité du module
* Utilisation du processeur et de la mémoire par module
* Utilisation du processeur et de la mémoire dans tous les modules
* Nombre de redémarrages de modules et chronologie des redémarrages.

# <a name="host"></a>[Hôte](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="Section Hôte du workbook des détails de l’appareil." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

La vue **Hôte** présente les métriques du module edgeAgent. Il comporte des informations telles que :

* Informations sur les versions des composants de l’hôte
* Uptime
* Utilisation du processeur, de la mémoire et de l’espace disque au niveau de l’hôte

# <a name="live-logs"></a>[Journaux dynamiques](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="Accédez aux journaux dynamiques par l’intermédiaire du workbook des détails de l’appareil." lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

Ce workbook s’intègre directement à l’expérience de la résolution des problèmes sur le portail. Cliquez sur le bouton **Résoudre les problèmes en temps réel** pour accéder à l’écran de résolution des problèmes. Là, vous pouvez facilement afficher les journaux des modules extraits de l’appareil, à la demande. L’intervalle de temps est automatiquement défini sur la plage horaire du workbook, vous êtes ainsi immédiatement dans le contexte temporel. Vous pouvez également redémarrer n’importe quel module à partir de cette expérience.

---

## <a name="iot-edge-health-snapshot-workbook"></a>Workbook d’instantané d’intégrité IoT Edge

Le workbook d’instantané d’intégrité est accessible à partir du workbook des vues de la flotte. Le workbook des vues de la flotte transmet certains paramètres nécessaires à l’initialisation de la vue d’instantané d’intégrité. Sélectionnez un nom d’appareil dans la cellule de la ruche pour afficher l’instantané d’intégrité de cet appareil.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="Accédez au workbook d’instantané d’intégrité en sélectionnant un appareil dans le workbook des vues de la flotte." lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::

L’instantané d’intégrité est composé de six signaux prêts à l’emploi :

* Messages en amont
* Messages locaux
* Longueur de la file d’attente
* Utilisation du disque :
* Utilisation du processeur au niveau de l’hôte
* Utilisation de la mémoire au niveau de l’hôte

Ces signaux sont mesurés par rapport à des seuils configurables permettant de déterminer si un appareil est sain ou non. Vous pouvez ajuster les seuils ou ajouter de nouveaux signaux en modifiant le workbook. Pour en savoir plus sur les personnalisations de workbooks, consultez la section suivante.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="Affichez le workbook d’instantané d’intégrité." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>Personnaliser les workbooks

Les [workbooks Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) sont extrêmement personnalisables. Vous pouvez modifier les modèles publics pour répondre à vos besoins. Toutes les visualisations sont pilotées par des requêtes [KQL](https://aka.ms/kql) axées sur les ressources dans la table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics). Consultez l’exemple ci-dessous qui modifie les seuils d’intégrité.

Pour démarrer la personnalisation d’un workbook, commencez par passer en mode édition. Sélectionnez le bouton **Modifier** dans la barre de menus du workbook.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="Entrez dans le mode d’édition d’un workbook." lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::

Les workbooks organisés utilisent beaucoup les groupes de workbooks. Vous devrez peut-être cliquer sur **Modifier** dans plusieurs groupes imbriqués avant de pouvoir afficher une requête de visualisation.

Enregistrez vos modifications dans un nouveau workbook. Vous pouvez [partager](../azure-monitor/visualize/workbooks-access-control.md) les workbooks enregistrés avec votre équipe, ou les [déployer programmatiquement](../azure-monitor/visualize/workbooks-automate.md) dans le cadre des déploiements de ressources de votre organisation.

Par exemple, vous pouvez avoir envie de modifier les seuils déterminant le moment où un appareil est considéré sain ou non sain. Vous avez la possibilité de le faire en explorant le modèle du workbook des vues de la flotte jusqu’à l’élément de requête **device-health-graph** qui comprend tous les seuils des métriques auxquels ce workbook compare un appareil.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="Poursuivez en ouvrant le mode d’édition des composants imbriqués jusqu’à atteindre l’élément de requête." lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::

## <a name="next-steps"></a>Étapes suivantes

Personnalisez votre solution de supervision avec des [règles d’alerte](how-to-create-alerts.md) et des [métriques à partir de modules personnalisés](how-to-add-custom-metrics.md).
