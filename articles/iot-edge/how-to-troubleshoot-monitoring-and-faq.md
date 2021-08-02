---
title: Résolution des problèmes de supervision et questions fréquentes – Azure IoT Edge
description: Résolution des problèmes d’intégration à Azure Monitor et questions fréquentes
author: veyalla
manager: philmea
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 09475cf4ee2c78596e3c93f408fc88c16e1a751e
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904431"
---
# <a name="faq-and-troubleshooting"></a>FAQ et résolution des problèmes

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>Le module du collecteur ne peut pas collecter les métriques depuis les points de terminaison intégrés

### <a name="check-if-modules-are-on-the-same-docker-network"></a>Vérifier si les modules se trouvent sur le même réseau Docker

Le module du collecteur de métriques s’appuie sur le programme de résolution DNS incorporé de Docker pour les réseaux définis par l’utilisateur. Le programme de résolution DNS fournit l’adresse IP des points de terminaison de métriques qui incluent le nom du module. Par exemple, *http://**edgeHub**:9600/metrics*.

Lorsque les modules ne s’exécutent pas dans le même espace de noms réseau, ce mécanisme échoue. Par exemple, certains scénarios exigent l’exécution des modules sur le réseau hôte. La collecte échoue dans de tels scénarios si le module du collecteur de métriques se trouve sur un autre réseau.

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>Vérifier que la variable d’environnement *httpSettings__enabled* n’a pas la valeur *false*

Les points de terminaison de métriques intégrés utilisent le protocole HTTP. Ils ne sont pas disponibles, même au sein du réseau du module, si le protocole HTTP est explicitement désactivé via le paramètre de la variable d’environnement.

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>Définir la variable d’environnement *NO_PROXY* si vous utilisez un serveur proxy HTTP

Pour plus d’informations, consultez [Considérations relatives au proxy](how-to-collect-and-transport-metrics.md#proxy-considerations).

## <a name="how-do-i-collect-logs-along-with-metrics"></a>Comment collecter les journaux avec les métriques ?

Vous pouvez utiliser [les fonctionnalités intégrées d’extraction de journaux](how-to-retrieve-iot-edge-logs.md). Un exemple de solution utilisant les fonctionnalités intégrées de récupération des journaux est disponible sur [ **https://aka.ms/iot-elms** ](https://aka.ms/iot-elms).

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>Pourquoi je ne vois pas les métriques de l’appareil dans la page Métriques du portail Azure ?

Pour le moment, la technologie des [métriques natives](../azure-monitor/essentials/data-platform-metrics.md) d’Azure Monitor ne prend pas en charge directement le format de données Prometheus. Les métriques figurant dans les journaux sont actuellement mieux adaptées pour les métriques IoT Edge de par :

* La prise en charge native du format des métriques Prometheus via la table *InsightsMetrics* standard.
* Le traitement des données via [KQL](https://aka.ms/kql) pour les visualisations et les alertes.

L’utilisation de Log Analytics en tant que base de données des métriques est la raison pour laquelle les métriques s’affichent dans la page des **Journaux** du portail Azure plutôt que dans celle des **Métriques**.

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>Comment configurer le collecteur de métriques dans un déploiement en couches ?

Le collecteur de métriques ne dispose d’aucune fonctionnalité de découverte de service. Nous vous recommandons d’inclure le module dans la couche de déploiement de base ou *inférieure*. Incluez dans la configuration du module tous les points de terminaison de métriques avec lesquels le module peut être déployé. Si un module n’apparaît pas dans un déploiement final, mais que son point de terminaison s’affiche dans la liste des collections, le collecteur essaiera de collecter, il échouera et continuera.

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>Comment augmenter la solution de supervision avec des métriques personnalisées ?

Consultez l’article sur les [métriques personnalisées](how-to-add-custom-metrics.md).

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>Comment savoir à quel appareil appartient une métrique en particulier ?

Encodez les informations de l’appareil dans les étiquettes des métriques. Pour plus d'informations, consultez [Conventions d'affectation de noms](how-to-add-custom-metrics.md#naming-conventions).

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>Comment créer une règle d’alerte s’étendant aux appareils de plusieurs hubs IoT ?

Lorsque vous [créez une règle d’alerte](how-to-create-alerts.md#create-an-alert-rule), vous pouvez [modifier sa portée](how-to-create-alerts.md#select-alert-rule-scope) jusqu’à un groupe de ressources ou un abonnement. La règle d’alerte s’appliquera alors à tous les hubs IoT de cette étendue.

## <a name="alerts-arent-firing-when-they-should"></a>Les alertes ne se déclenchent pas quand elles le devraient

Lorsque vous créez une règle d’alerte, assurez-vous que la logique d’alerte se déclenche en vérifiant le graphe d’aperçu.

Si vous ne parvenez pas à trouver le problème, créez un [incident de support technique](https://azure.microsoft.com/support/create-ticket/) pour le service **Log Analytics**.

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>Mon appareil n’apparaît pas dans le workbook de supervision

Le workbook s’appuie sur la liaison existant entre les métriques de l’appareil et le hub IoT approprié au moyen de *ResourceId*. Vérifiez que le collecteur de métriques est [configuré](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) avec le *ResourceId* adéquat.

À l’aide des journaux du module du collecteur de métriques, contrôlez l’envoi de métriques par l’appareil pendant l’intervalle de temps sélectionné.

Gardez à l’esprit qu’un délai d’ingestion de quelques minutes peut être observé avant que les métriques ne s’affichent.

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>J’ai trouvé un bogue ou j’ai une question sur les métriques affichées dans le workbook

Ouvrez un problème dans le [dépôt GitHub Azure IOT Edge](https://github.com/azure/iotedge/issues) en mentionnant « [workbook-monitor] » dans le titre.

Le modèle des workbooks est [disponible publiquement sur GitHub](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub). Les demandes de tirage (pull request) avec des améliorations ou des correctifs sont très appréciés !

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>Je ne vois pas les workbooks dans les modèles publics

Veillez à consulter la page **Workbooks** dans la page de votre hub IoT dans le portail, et non dans votre espace de travail Log Analytics.

Si vous ne voyez toujours pas les workbooks, essayez d’utiliser l’environnement de préproduction du portail Azure : [`https://ms.portal.azure.com`](https://ms.portal.azure.com). Parfois, les mises à jour de workbook prennent plus de temps pour s’afficher dans l’environnement de production, mais elles sont disponibles en préproduction.

:::zone-end
