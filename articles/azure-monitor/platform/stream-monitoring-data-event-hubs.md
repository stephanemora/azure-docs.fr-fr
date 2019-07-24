---
title: Diffuser des données de surveillance Azure vers Event Hubs
description: Découvrez comment diffuser vos données de surveillance Azure vers un hub d’événements afin de les intégrer à un système SIEM ou à un outil d’analytique partenaire.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: nikiest
ms.subservice: ''
ms.openlocfilehash: 8a4de244d0fa07bfc162625f577015317fca7e6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069335"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe

Cet article vous aide à configurer différentes couches à partir des données de votre environnement Azure, en vue de les envoyer vers un espace de noms ou un hub d’événements Event Hubs, où elles pourront être collectées par un outil externe.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Quelles données puis-je envoyer vers un hub d’événements ?

Au sein de votre environnement Azure, il existe plusieurs « couches » de données de surveillance. La méthode pour y accéder varie légèrement selon les couches. En règle générale, ces couches peuvent être décrites ainsi :

- **Données de surveillance de l’application :** données concernant les performances et la fonctionnalité du code que vous avez écrit et qui est exécuté dans Azure. Il peut s’agir, par exemple, de traces de performances, de journaux d’activité d’applications ou de télémétrie utilisateur. Les données de surveillance de l’application sont généralement regroupées de l’une des manières suivantes :
  - En instrumentant votre code avec un SDK tel que le [SDK Application Insights](../../azure-monitor/app/app-insights-overview.md)
  - En exécutant un agent de supervision qui écoute les nouveaux journaux d’activité d’application sur la machine qui exécute votre application, tel que [l’agent de diagnostic Azure pour Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou [l’agent de diagnostic Azure pour Linux](../../virtual-machines/extensions/diagnostics-linux.md)
- **Données de surveillance du système d’exploitation invité :** données concernant le système d’exploitation sur lequel votre application est exécutée. Il peut s’agir, par exemple, de journaux système Linux ou d’événements système Windows. Pour collecter ce type de données, vous devez installer un agent tel que [l’agent de diagnostic Azure pour Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou [l’agent de diagnostic Azure pour Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Données de surveillance des ressources Azure :** données sur le fonctionnement d’une ressource Azure. Pour certains types de ressources Azure, telles que les machines virtuelles, il existe un système d’exploitation invité et des applications qui permettent de surveiller ce qui se passe dans le service Azure. Pour d’autres ressources Azure, telles que les groupes de sécurité réseau, les données de surveillance des ressources constituent la couche de données la plus élevée (dans la mesure où aucun système d’exploitation invité ni aucune application ne sont exécutés sur ces ressources). Ces données peuvent être collectées à l’aide des [paramètres de diagnostic des ressources](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Données de surveillance de l’abonnement Azure :** données concernant le fonctionnement et la gestion d’un abonnement Azure, mais aussi données concernant l’intégrité et le fonctionnement d’Azure. Le [journal d’activité](./../../azure-monitor/platform/activity-logs-overview.md) contient la plupart des données de surveillance d’abonnement, telles que les incidents d’intégrité de service et les audits d’Azure Resource Manager. Vous pouvez collecter ces données à l’aide d’un profil de journal.
- **Données de surveillance du locataire Azure :** données concernant le fonctionnement des services Azure au niveau du locataire, tels qu’Azure Active Directory. Les connexions et les audits d’Azure Active Directory sont des exemples de données de surveillance de locataire. Ces données peuvent être collectées à l’aide d’un paramètre de diagnostic de locataire.

Vous pouvez envoyer les données de toutes les couches vers un hub d’événements, duquel elles pourront être extraites par un outil partenaire. Certaines sources peuvent être configurées pour envoyer des données directement à un hub d’événements, tandis qu’un autre processus comme une application logique peut être nécessaire pour récupérer les données requises. Les sections suivantes expliquent comment configurer les données de chaque couche de sorte qu’elles soient diffusées vers un hub d’événements. Ces étapes supposent que cette couche contient déjà des ressources à surveiller.

## <a name="set-up-an-event-hubs-namespace"></a>Configurer un espace de noms Event Hubs

Avant de commencer, vous devez [créer un espace de noms et un hub Event Hubs](../../event-hubs/event-hubs-create.md). Cet espace de noms et ce hub d’événements constituent la destination de toutes vos données de surveillance. Un espace de noms Event Hubs est un regroupement logique de hubs d’événements qui partagent une même stratégie d’accès, tout comme un compte de stockage contient des objets blob. Notez les points suivants concernant l’espace de noms Event Hubs et les hubs d’événements que vous créez :
* Nous recommandons d’utiliser un espace de noms Event Hubs standard.
* En règle générale, une seule unité de débit est nécessaire. Si vous avez besoin de plus pour répondre à l’augmentation de l’utilisation de votre journal, vous pouvez augmenter manuellement le nombre d’unités de débit pour l’espace de noms ou activer l’inflation automatique.
* Le nombre d’unités de débit vous permet d’augmenter l’échelle de débit de vos hubs d’événements. Le nombre de partitions vous permet de paralléliser la consommation sur un grand nombre de consommateurs. Une seule partition peut atteindre jusqu’à 20 Mbits/s, soit environ 20 000 messages par seconde. En fonction de l’outil qui consomme les données, la consommation simultanée de plusieurs partitions risque de ne pas être prise en charge. Si vous n’êtes pas sûr du nombre de partitions à définir, il est recommandé de commencer avec quatre partitions.
* Il est également recommandé de définir la conservation des messages de votre hub d’événements sur 7 jours. Si l’outil consommateur est en panne pendant plus d’un jour, cela garantit qu’il pourra reprendre là où il s’est arrêté (pour les événements des 7 derniers jours).
* Il est recommandé d’utiliser le groupe de consommateurs par défaut pour votre hub d’événements. Il n’est pas nécessaire de créer d’autres groupes de consommateurs ou d’utiliser un groupe de consommateurs distinct, sauf si deux outils doivent utiliser les mêmes données d’un même hub d’événements.
* Pour le journal des activités Azure, vous pouvez choisir un espace de noms Event Hubs pour qu’Azure Monitor y crée un hub d’événements appelé « insights-logs-operational-logs ». Pour les autres types de journaux d’activité, vous pouvez soit choisir un hub d’événements existant (ce qui vous permet de réutiliser le hub d’événements insights-logs-operational-logs), soit créer un hub d’événements par catégorie de journal d’activité à l’aide d’Azure Monitor.
* En règle générale, les ports de sortie 5671 à 5672 doivent être ouverts sur l’ordinateur ou le réseau qui consomme les données du hub d’événements.

Consultez également le [Forum aux questions (FAQ) sur Azure Event Hubs](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Données de surveillance du locataire Azure

Les données de surveillance des locataires Azure ne sont actuellement disponibles que pour Azure Active Directory. Vous pouvez utiliser les données des [rapports Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md), qui contiennent l’historique des connexions et la piste d’audit des modifications apportées à un locataire particulier.

### <a name="azure-active-directory-data"></a>Données Azure Active Directory

Pour envoyer des données à partir du journal Azure Active Directory dans un espace de noms Event Hubs, vous configurer un paramètre de diagnostic de locataire sur votre locataire AAD. [Suivez ce guide](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) pour configurer un paramètre de diagnostic de locataire.

## <a name="azure-subscription-monitoring-data"></a>Données de surveillance de l’abonnement Azure

Les données de surveillance d’abonnement Azure sont disponibles dans le [journal d’activité Azure](./../../azure-monitor/platform/activity-logs-overview.md). Celui-ci contient les opérations de création, de mise à jour et de suppression du Gestionnaire des ressources, les changements d’[intégrité du service Azure](../../service-health/service-health-overview.md) qui peuvent influer sur les ressources de votre abonnement, les transitions d’état d’[intégrité des ressources](../../service-health/resource-health-overview.md), et plusieurs autres types d’événements au niveau de l’abonnement. [Cet article décrit toutes les catégories d’événements qui s’affichent dans le journal d’activité Azure](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Données du journal d'activité

Pour envoyer les données du journal d’activité Azure vers un espace de noms Event Hubs, vous devez configurer un profil de journal dans votre abonnement. [Suivez ce guide](./activity-logs-stream-event-hubs.md) pour configurer un profil de journal dans votre abonnement. Vous devez effectuer cette configuration pour chaque abonnement que vous souhaitez surveiller.

> [!TIP]
> Un profil de journal permet uniquement de sélectionner un espace de noms Event Hubs, dans lequel un hub d’événements est créé puis nommé « insights-operational-logs ». Il n’est pas encore possible de spécifier un nom personnalisé pour un hub d’événements dans un profil de journal.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Métriques des ressources et journaux de diagnostic Azure

Les ressources Azure émettent deux types de données de surveillance :
1. [Journaux de diagnostic des ressources](diagnostic-logs-overview.md)
2. [Métriques](data-platform.md)

Ces deux types de données sont envoyés à un hub d’événements à l’aide d’un paramètre de diagnostic des ressources. [Suivez ce guide](diagnostic-logs-stream-event-hubs.md) pour définir un paramètre de diagnostic dans une ressource particulière. Définissez un paramètre de diagnostic des ressources pour chacune des ressources dont vous voulez collecter les journaux d’activité.

> [!TIP]
> Vous pouvez utiliser Azure Policy pour faire en sorte que toutes les ressources de l’étendue spécifiée soient toujours configurées avec un paramètre de diagnostic [à l’aide de l’effet DeployIfNotExists de la règle de stratégie](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Données du système d’exploitation invité

Pour envoyer les données de surveillance des systèmes d’exploitation invités vers un hub d’événements, vous devez installer un agent. Que ce soit pour Windows ou pour Linux, vous devez spécifier dans un fichier config les données à envoyer au hub d’événements, ainsi que le hub d’événements vers lequel envoyer les données. Ensuite, vous devez passer ce fichier config à l’agent qui est exécuté sur la machine virtuelle.

### <a name="linux-data"></a>Données Linux

[L’agent de diagnostic Azure pour Linux](../../virtual-machines/extensions/diagnostics-linux.md) peut être utilisé pour envoyer les données de surveillance d’une machine Linux vers un hub d’événements. Pour cela, ajoutez le hub d’événements comme récepteur dans les paramètres protégés de votre fichier config LAD. [Consultez cet article pour en savoir plus sur l’ajout du récepteur de hub d’événements à l’agent de diagnostic Azure pour Linux](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Il n’est pas possible de configurer le streaming des données de surveillance des systèmes d’exploitation invités vers un hub d’événements dans le portail. Au lieu de cela, vous devez modifier manuellement le fichier config.

### <a name="windows-data"></a>Données Windows

[L’agent de diagnostic Azure pour Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) peut être utilisé pour envoyer les données de surveillance d’une machine Windows vers un hub d’événements. Pour cela, ajoutez le hub d’événements comme récepteur dans la section privateConfig du fichier config WAD. [Consultez cet article pour en savoir plus sur l’ajout du récepteur de hub d’événements à l’agent de diagnostic Azure pour Windows](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Il n’est pas possible de configurer le streaming des données de surveillance des systèmes d’exploitation invités vers un hub d’événements dans le portail. Au lieu de cela, vous devez modifier manuellement le fichier config.

## <a name="application-monitoring-data"></a>Données de surveillance de l’application

Les données de surveillance de l’application nécessitent que votre code soit instrumenté avec un SDK. Il n’existe donc pas de solution toute faite pour router les données de surveillance de l’application vers un hub d’événements dans Azure. Toutefois, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) est un service qui peut être utilisé pour collecter des données Azure au niveau de l’application. Si vous utilisez Application Insights, vous pouvez diffuser des données de surveillance vers un hub d’événements en procédant de la manière suivante :

1. [Configurez l’exportation continue](../../azure-monitor/app/export-telemetry.md) des données Application Insights vers un compte de stockage.

2. Configurez une application logique déclenchée par minuteur qui [tire (pull) les données du stockage d’objets blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) et [les envoie (push) en tant que message au hub d’événements](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Que puis-je faire avec les données de surveillance qui sont envoyées à mon hub d’événements ?

Le routage de vos données de surveillance vers un hub d’événements avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM ou des outils de surveillance partenaires. La plupart des outils nécessitent la chaîne de connexion du hub d’événements, ainsi que certaines autorisations de votre abonnement Azure, pour lire les données du hub d’événements. Voici une liste non exhaustive d’outils avec intégration Azure Monitor :

* **IBM QRadar** : le module DSM Microsoft Azure et le protocole Microsoft Azure Event Hubs sont disponibles au téléchargement sur le [site web du support IBM](https://www.ibm.com/support). Pour plus d’informations sur l’intégration à Azure [cliquez ici](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - En fonction de votre configuration Splunk, deux approches sont possibles :
    1. [Le module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/) est un projet open source disponible dans Splunkbase. [La documentation est disponible ici](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk (par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk), vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk en utilisant [cette fonction qui est déclenchée par les nouveaux messages dans le hub d’événements](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** : les instructions pour configurer SumoLogic de manière à utiliser les données d’un hub d’événements sont [disponibles ici](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** : le connecteur intelligent ArcSight Azure Event Hub est disponible dans la [collection de connecteurs intelligents ArcSight ici](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Serveur Syslog** : si vous souhaitez transmettre en continu les données d’Azure Monitor directement à un serveur syslog, vous pouvez consulter [ce référentiel GitHub](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Étapes suivantes
* [Archiver le journal d’activité dans un compte de stockage](../../azure-monitor/platform/archive-activity-log.md)
* [Lire la présentation du journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Définir une alerte basée sur un événement de journal d’activité](../../azure-monitor/platform/alerts-log-webhook.md)


