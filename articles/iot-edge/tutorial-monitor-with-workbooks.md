---
title: 'Tutoriel : Classeurs Azure Monitor pour IoT Edge'
description: Découvrez comment effectuer le monitoring des modules et des appareils IoT Edge avec des classeurs Azure Monitor pour IoT.
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/13/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6ba79aa63700a35e79d49febad8510e283b35441
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207274"
---
# <a name="tutorial-monitor-iot-edge-devices"></a>Tutoriel : Monitoring des appareils IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Utilisez des classeurs Azure Monitor pour effectuer le monitoring de l’intégrité et du niveau de performance de vos déploiements Azure IoT Edge.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Découvrez quelles métriques sont partagées par les appareils IoT Edge et comment elles sont gérées par le module du collecteur de métriques.
> * Déployez le module du collecteur de métriques sur un appareil IoT Edge.
> * Consultez des visualisations curées des métriques collectées à partir de l’appareil.

## <a name="prerequisites"></a>Prérequis

Il vous faut un appareil IoT Edge sur lequel le module de capteur de température simulé est déployé. Si vous ne possédez pas d’appareil prêt, suivez la procédure décrite dans [Déploiement d’un premier module IoT Edge sur un appareil virtuel Linux](quickstart-linux.md) pour en créer un avec une machine virtuelle.

## <a name="understand-iot-edge-metrics"></a>Présentation des métriques IoT Edge

Chaque appareil IoT Edge s’appuie sur deux modules, les *modules du runtime*, qui gèrent le cycle de vie et la communication de tous les autres modules sur un appareil : **l’agent IoT Edge** et le **hub IoT Edge**. Pour plus d’informations sur ces modules, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Les deux modules du runtime créent des métriques permettant d’effectuer à distance le monitoring du niveau de performance d’un appareil IoT Edge ou de ses différents modules. L’agent IoT Edge signale l’état de chacun des modules et de l’appareil hôte. Il crée donc des métriques telles que la durée de bon fonctionnement d’un module ou la quantité de mémoire RAM employée et le pourcentage de processeur exploités sur l’appareil. Le hub IoT Edge, lui, signale les communications sur l’appareil. Il crée donc des métriques telles que le nombre total de messages envoyés et reçus ou le temps nécessaire à la résolution d’une méthode directe. Pour connaître la liste complète des métriques disponibles, consultez [Accès aux métriques intégrées](how-to-access-built-in-metrics.md).

Ces métriques sont exposées automatiquement par les deux modules. Vous pouvez ainsi créer vos propres solutions pour accéder à ces métriques et créer des rapports. Pour faciliter les choses, Microsoft fournit le module [azureiotedge-metrics-collector](https://hub.docker.com/_/microsoft-azureiotedge-metrics-collector) qui gère ce processus pour ceux qui ne possèdent pas de solution personnalisée ou n’en ont pas besoin. Ce module collecte des métriques auprès des deux modules du runtime et de tous les autres modules dont vous souhaitez effectuer le monitoring, et les transfère hors de l’appareil.

Le module du collecteur de métriques fonctionne de deux manières pour envoyer les métriques vers le cloud. La première option, que nous allons utiliser dans ce tutoriel, consiste à les envoyer directement à Log Analytics. La deuxième option n’est recommandée que si les stratégies de mise en réseau l’exigent : il s’agit d’envoyer les métriques via IoT Hub, puis de configurer un itinéraire pour transmettre les messages de métriques à Log Analytics. Dans les deux cas, les métriques sont consultables par le biais de classeurs Azure Monitor une fois qu’elles se trouvent dans l’espace de travail Log Analytics.

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Un espace de travail Log Analytics est nécessaire pour collecter les données de métriques. Il fournit un langage de requête et une intégration avec Azure Monitor pour vous permettre d’effectuer le monitoring de vos appareils.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Recherchez et sélectionnez **Espaces de travail Log Analytics**.

1. Sélectionnez **Créer**, puis suivez les invites pour créer un espace de travail.

1. Une fois l’espace de travail créé, sélectionnez **Accéder à la ressource**.

1. Sous **Paramètres** dans le menu principal, sélectionnez **Gestion des agents**.

1. Copiez les valeurs **ID de l’espace de travail** et **Clé primaire**. Vous les utiliserez plus loin dans ce tutoriel pour configurer le module du collecteur de métriques de sorte qu’il envoie les métriques à cet espace de travail.

## <a name="retrieve-your-iot-hub-resource-id"></a>Récupération de l’ID de ressource du IoT

Quand vous configurez le module du collecteur de métriques, vous lui donnez l’ID de ressource Azure Resource Manager de votre IoT Hub. Récupérez maintenant cet ID.

1. Sur le Portail Azure, accédez à votre hub IoT.

1. Sous **Paramètres** dans le menu de gauche, sélectionnez **Propriétés**.

1. Copiez la valeur **ID de la ressource**. Elle suit le format `/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Devices/IoTHubs/<iot_hub_name>`.

## <a name="deploy-the-metrics-collector-module"></a>Déploiement du module du collecteur de métriques

Déployez le module du collecteur de métriques sur chacun des appareils dont vous souhaitez effectuer le monitoring. Il s’exécute sur l’appareil comme n’importe quel autre module, et surveille les points de terminaison qui lui sont attribués pour collecter les métriques et les envoyer dans le cloud.

Pour déployer et configurer le module du collecteur, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.

1. Dans le menu de gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.

1. Sélectionnez l’ID de l’appareil cible dans la liste des appareils IoT Edge pour ouvrir la page Détails de l’appareil.

1. Dans la barre de menus supérieure, sélectionnez **Définir les modules** pour ouvrir la page de déploiement de modules en trois étapes.

1. La première étape du déploiement de modules sur le portail consiste à déclarer les **Modules** qui doivent se trouver sur un appareil. Si vous utilisez l’appareil que vous avez créé dans le guide de démarrage rapide, **SimulatedTemperatureSensor** apparaît déjà dans la liste. Si ce n’est pas le cas, ajoutez-le maintenant :

   1. Sélectionnez **Ajouter**, puis choisissez **Module de la Place de marché** dans le menu déroulant.

   1. Recherchez et sélectionnez **SimulatedTemperatureSensor**.

1. Ajoutez et configurez le module du collecteur de métriques :

   1. Sélectionnez **Ajouter**, puis choisissez **Module de la Place de marché** dans le menu déroulant.
   1. Recherchez et sélectionnez **Collecteur de métriques IoT Edge**.
   1. Sélectionnez le module du collecteur de métriques dans la liste des modules pour ouvrir sa page Détails de la configuration.
   1. Accédez à l’onglet **Variables d’environnement**.
   1. Utilisez les valeurs suivantes :

      | Nom | Valeur |
      | ---- | ----- |
      | **ResourceId** | ID de ressource du hub IoT que vous avez récupéré dans une section précédente. |
      | **UploadTarget** | `AzureMonitor` |
      | **LogAnalyticsWorkspaceId** | ID de l’espace de travail Log Analytics que vous avez récupéré dans une section précédente. |
      | **LogAnalyticsSharedKey** | Clé Log Analytics que vous avez récupérée dans une section précédente. |

   1. Supprimez la variable d’environnement **OtherConfig**, un espace réservé pour les options de configuration supplémentaires que vous souhaiterez peut-être ajouter ultérieurement. Elle n’est pas nécessaire pour ce tutoriel.
   1. Sélectionnez **Mettre à jour** pour enregistrer vos modifications.

1. Sélectionnez **Suivant : Itinéraires** pour passer à la deuxième étape du déploiement de modules.

1. Le portail ajoute automatiquement un itinéraire pour le collecteur de métriques. Vous pouvez utiliser cet itinéraire si vous avez configuré le module du collecteur de sorte qu’il envoie les métriques par le biais de IoT Hub. Dans ce tutoriel cependant, nous envoyons directement les métriques à Log Analytics. Il n’est donc pas nécessaire. Supprimez l’itinéraire **FromMetricsCollectorToUpstream**.

1. Sélectionnez **Vérifier + créer** pour passer à l’étape finale du déploiement des modules.

1. Sélectionnez **Créer** pour terminer le déploiement.

Une fois le déploiement du module effectué, vous revenez à la page Détails de l’appareil sur laquelle vous pouvez voir quatre modules indiqués comme **Spécifié dans le déploiement**. Il peut s’écouler quelques instants avant que les quatre modules n’apparaissent comme **Signalé par l’appareil**. Cette mention signifie qu’ils ont démarré correctement et ont bien signalé leur état à IoT Hub. Actualisez la page pour afficher l’état le plus récent.

## <a name="monitor-device-health"></a>Monitoring de l’intégrité de l’appareil

L’affichage des classeurs de monitoring des appareils peut prendre jusqu’à quinze minutes. Une fois déployé, le module du collecteur de métriques commence à envoyer des messages de métriques à Log Analytics, où ils sont organisés dans une table. L’ID de la ressource IoT Hub fourni lie les métriques ingérées au hub auquel elles appartiennent. Par conséquent, les classeurs IoT Edge curés peuvent récupérer les métriques en interrogeant la table de métriques avec l’ID de la ressource.

Azure Monitor fournit trois modèles de classeurs par défaut pour IoT :

* Le classeur **Vue de la flotte IoT Edge** affiche une vue d’ensemble des appareils actifs, permettant d’identifier les appareils non sains et d’explorer au niveau du détail le fonctionnement de chaque appareil. Il présente également les alertes générées à partir de toutes les règles d’alerte créées.
* Le classeur **Détails de l’appareil IoT Edge** fournit des visualisations autour de trois catégories : messages, modules et hôte. La vue Messages permet de visualiser l’itinéraire des messages pour un appareil et donne l’intégrité globale du système de messagerie. La vue Modules montre le niveau de performance des différents modules d’un appareil. La vue Hôte présente des informations sur l’appareil hôte, notamment des informations de version sur les composants hôtes et l’utilisation des ressources.
* Le classeur **Instantané d’intégrité IoT Edge** mesure les signaux d’un appareil par rapport à des seuils configurables pour déterminer si l’appareil est sain ou non. Il n’est accessible qu’à partir du classeur Vue de la flotte, qui passe les paramètres nécessaires pour initialiser l’instantané d’intégrité d’un appareil en particulier.

### <a name="explore-the-fleet-view-and-health-snapshot-workbooks"></a>Exploration des classeurs Vue de la flotte et Instantané d’intégrité

Le classeur Vue de la flotte affiche tous les appareils. Il offre également la possibilité de sélectionner des appareils spécifiques pour voir leur instantané d’intégrité. Pour explorer les visualisations du classeur, procédez comme suit :

1. Revenez sur la page de votre hub IoT sur le Portail Azure.

1. Faites défiler le menu principal pour trouver la section **Monitoring**, puis sélectionnez **Classeurs**.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/workbooks-gallery.png" alt-text="Sélection de Classeurs pour ouvrir la galerie de classeurs Azure Monitor.":::

1. Sélectionnez le classeur **Vue de la flotte IoT Edge**.

1. L’appareil qui exécute le module du collecteur de métriques y apparaît comme **sain** ou **non sain**.

1. Sélectionnez le nom de l’appareil pour ouvrir **l’instantané d’intégrité IoT Edge** et afficher des détails spécifiques sur son intégrité.

1. Sur n’importe quel graphe temporel, utilisez les icônes de direction qui figurent sous l’axe X ou cliquez sur le graphe et faites glisser le curseur pour modifier l’intervalle de temps.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/health-snapshot-custom-time-range.png" alt-text="Clic et glisser-déplacer ou utilisation des icônes de direction sur n’importe quel graphe pour modifier l’intervalle de temps.":::

1. Fermez le classeur Instantané d’intégrité. Sélectionnez **Classeurs** dans le classeur Vue de la flotte pour revenir à la galerie de classeurs.

### <a name="explore-the-device-details-workbook"></a>Exploration du classeur Détails de l’appareil

Le classeur Détails de l’appareil affiche des informations sur le niveau de performance d’un appareil donné. Pour explorer les visualisations du classeur, procédez comme suit :

1. Dans la galerie de classeurs, sélectionnez le classeur **Détails de l’appareil IoT Edge**.

1. La première page qui s’affiche dans le classeur Détails de l’appareil est la vue **Messages** avec l’onglet **Acheminement** sélectionné.

   Sur la gauche, un tableau affiche les itinéraires sur l’appareil, organisés par point de terminaison. Pour notre appareil, nous voyons que le point de terminaison **en amont** (terme spécial utilisé pour l’acheminement vers IoT Hub) reçoit des messages de la sortie **temperatureOutput** du module de capteur de température simulé.

   Sur la droite, un graphe effectue le suivi du nombre de clients connectés au fil du temps. Vous pouvez cliquer et faire glisser le graphe pour modifier l’intervalle de temps.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-routing.png" alt-text="Sélection de la vue Messages pour afficher l’état des communications sur l’appareil.":::

1. Sélectionnez l’onglet **Graphe** pour afficher une autre visualisation des itinéraires. Sur la page du graphe, vous pouvez glisser-déplacer les différents points de terminaison pour le réorganiser. Cette fonctionnalité est utile en présence de nombreux itinéraires à visualiser.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-graph.png" alt-text="Sélection de la vue du graphe pour afficher un graphe interactif des itinéraires de l’appareil.":::

1. L’onglet **Intégrité** signale tous les problèmes liés aux messages, par exemple les messages supprimés et les clients déconnectés.

1. Sélectionnez la vue **Modules** pour afficher l’état de tous les modules déployés sur l’appareil. Vous pouvez sélectionner chacun des modules pour afficher des détails sur la quantité de processeur et de mémoire qu’ils utilisent.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-modules-availability.png" alt-text="Sélection de la vue Modules pour afficher l’état de chacun des modules déployés sur l’appareil.":::

1. Sélectionnez la vue **Hôte** pour afficher des informations sur l’appareil hôte, notamment son système d’exploitation, la version du démon IoT Edge et l’utilisation des ressources.

## <a name="view-module-logs"></a>Affichage des journaux des modules

Si, après avoir consulté les métriques d’un appareil, vous souhaitez approfondir les choses, vous pouvez inspecter les modules un par un. IoT Edge fournit une assistance à la résolution des problèmes sur le Portail Azure avec une fonctionnalité de journaux dynamiques des modules.

1. Dans le classeur Détails de l’appareil, sélectionnez **Résoudre les problèmes en direct**.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-troubleshoot-live.png" alt-text="Sélection du bouton Résoudre les problèmes en direct dans le coin supérieur droit du classeur Détails de l’appareil.":::

1. La page de résolution des problèmes s’ouvre sur les journaux **edgeAgent** de l’appareil IoT Edge. Si vous avez sélectionné un intervalle de temps spécifique dans le classeur Détails de l’appareil, ce paramètre est transmis à la page de résolution des problèmes.

1. Utilisez le menu déroulant pour basculer vers les journaux des autres modules en cours d’exécution sur l’appareil. Sélectionnez le bouton **Redémarrer** pour redémarrer un module.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/troubleshoot-device.png" alt-text="Utilisation du menu déroulant pour afficher les journaux des différents modules et du bouton Redémarrer pour redémarrer les modules.":::

La page de résolution des problèmes est également accessible à partir de la page Détails d’un appareil IoT Edge. Pour plus d’informations, consultez [Résolution des problèmes des appareils IoT Edge sur le Portail Azure](troubleshoot-in-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous suivez les autres tutoriels, conservez le module de collecteur de métriques sur vos appareils et revenez sur ces classeurs. Vous verrez ainsi comment les informations changent lorsque vous ajoutez des modules et un acheminement plus complexes.

Passez au tutoriel suivant. Vous y configurez votre environnement de développement pour commencer à déployer des modules personnalisés sur vos appareils.

> [!div class="nextstepaction"]
> [Développement de modules IoT Edge avec des conteneurs Linux](tutorial-develop-for-linux.md)
