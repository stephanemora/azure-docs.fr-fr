---
title: Surveiller les opérations, les événements et les compteurs pour une instance publique de Basic Load Balancer
titleSuffix: Azure Load Balancer
description: Découvrez comment activer la journalisation pour Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572777"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Journaux d’activité Azure Monitor pour Azure Standard Load Balancer

Vous pouvez utiliser différents types de journaux d’activité dans Azure pour gérer les instances de Standard Load Balancer et résoudre les problèmes associés. Les journaux peuvent être envoyés en streaming à un hub d’événements ou à un espace de travail Log Analytics. Tous les journaux peuvent être extraits à partir du Stockage Blob Azure et affichés dans différents outils, comme Excel et Power BI.  Pour en savoir plus sur les différents types de journaux d’activité, consultez la liste ci-dessous.

* **Journaux d’activité :** Vous pouvez utiliser l’article [Afficher les journaux d’activité pour superviser les actions sur les ressources](../azure-resource-manager/management/view-activity-logs.md) afin d’afficher toutes les activités soumises à vos abonnements Azure et leur état. Les journaux d’activité sont activés par défaut et peuvent être affichés dans le portail Azure. Ces journaux d’activité sont disponibles pour les instances Basic et Standard Load Balancer.
* **Métriques de Standard Load Balancer :** Vous pouvez utiliser ce journal pour interroger les métriques exportées sous forme de journaux pour votre instance d’Azure Standard Load Balancer. Ces journaux sont uniquement disponibles pour les instances de Standard Load Balancer.

> [!IMPORTANT]
> **Actuellement, les journaux d’événements de sonde d’intégrité et d’alertes Load Balancer ne sont pas fonctionnels et sont répertoriés parmi les [problèmes connus avec Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Les journaux d’activité ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour plus d’informations sur les modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Activation de la journalisation

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Activez la journalisation des événements et des sondes d’intégrité pour commencer à collecter les données disponibles dans ces journaux. Utilisez les étapes suivantes pour activer la journalisation.

Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne disposez pas déjà d'un équilibreur de charge, [créez un équilibreur de charge](./quickstart-load-balancer-standard-public-portal.md) avant de continuer.

1. Dans le portail, cliquez sur **Groupes de ressources**.
2. Sélectionnez **\<resource-group-name>** l’emplacement de votre équilibreur de charge.
3. Sélectionnez votre équilibreur de charge.
4. Sélectionnez **Journal d’activité** > **Paramètres de diagnostic**.
5. Dans le volet **Paramètres de diagnostic**, sous **Paramètres de diagnostic**, sélectionnez **+ Ajouter un paramètre de diagnostic**.
6. Dans le volet de création de **Paramètres de diagnostic**, entrez **myLBDiagnostic** dans le champ **Nom**.
7. Trois options s’offrent à vous pour les **Paramètres de diagnostic**.  Vous pouvez en choisir une, deux ou les trois, et configurer chacune d’elles selon vos besoins :
   * **Archiver dans un compte de stockage**
   * **Diffuser vers un hub d’événements**
   * **Envoyer à Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archiver dans un compte de stockage
    Vous avez besoin d’un compte de stockage déjà créé pour ce processus.  Pour créer un compte de stockage, consultez [Créez un compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Cochez la case en regard d’**Archiver dans un compte de stockage**.
    2. Sélectionnez **Configurer** pour ouvrir le volet **Sélectionnez un compte de stockage**.
    3. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel votre compte de stockage a été créé.
    4. Sélectionnez le nom de votre compte de stockage sous **Compte de stockage** dans la zone déroulante.
    5. Sélectionnez OK.

    ### <a name="stream-to-an-event-hub"></a>Diffuser vers un hub d’événements
    Vous avez besoin d’un hub d’événements déjà créé pour ce processus.  Pour créer un hub d’événements, consultez [Démarrage rapide : Créer un hub d’événements avec le portail Azure](../event-hubs/event-hubs-create.md)

    1. Cochez la case en regard de **Diffuser vers un hub d’événements**.
    2. Sélectionnez **Configurer** pour ouvrir le volet **Sélectionner un hub d’événements**.
    3. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel votre hub d’événements a été créé.
    4. **Sélectionnez un espace de noms de hub d’événements** dans la zone déroulante.
    5. **Sélectionnez un nom de stratégie de hub d’événements** dans la zone déroulante.
    6. Sélectionnez OK.

    ### <a name="send-to-log-analytics"></a>Envoyer à Log Analytics
    Vous devez avoir un espace de travail Log Analytics déjà créé et configuré pour ce processus.  Pour créer un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/learn/quick-create-workspace.md).

    1. Cochez la case en regard de l’option **Envoyer à Log Analytics**.
    2. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel se trouve votre espace de travail Log Analytics.
    3. Sélectionnez l’**Espace de travail Log Analytics** dans la zone déroulante.


8.  Sous la section **JOURNAL** du volet **MÉTRIQUE**, cochez la case en regard de : **AllMetrics**

9. Vérifiez que tout semble correct, puis cliquez sur **Enregistrer** en haut du volet de création de **Paramètres de diagnostic**.

## <a name="activity-log"></a>Journal d’activité

Le journal d’activité est généré par défaut. Il peut être configuré de manière à être exporté au niveau de l’abonnement [en suivant les instructions de cet article](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Pour en savoir plus sur ces journaux, lisez l’article [Afficher les journaux d’activité pour superviser les actions sur les ressources](../azure-resource-manager/management/view-activity-logs.md).

### <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure** : Récupérez les informations du journal d'activité par le biais d'Azure PowerShell, de l'interface de ligne de commande (CLI) Azure, de l'API REST Azure ou du portail Azure. Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI :** si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide de l’[intégration des journaux d’audit Azure pour Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés ou personnaliser les vues selon vos besoins.

## <a name="metrics-as-logs"></a>Métriques sous forme de journaux
En utilisant la fonctionnalité d’exportation des métriques sous forme de journaux fournie par Azure Monitor, vous pouvez exporter vos métriques Load Balancer. Ces métriques génèrent une entrée de journal pour chaque intervalle d’échantillonnage d’une minute.

L’exportation de métriques sous forme de journaux est activée au niveau de chaque ressource. Pour activer ces journaux, accédez au panneau Paramètres de diagnostic, filtrez par Groupe de ressources et sélectionnez l’instance de Load Balancer pour laquelle vous souhaitez activer l’exportation des métriques. Une fois la page Paramètres de diagnostic de Load Balancer affichée, sélectionnez AllMetrics pour exporter les métriques éligibles sous forme de journaux.

Consultez la section [Limitations](#limitations) de cet article pour connaître les limitations relatives à l’exportation de métriques.

### <a name="view-and-analyze-metrics-as-logs"></a>Afficher et analyser les métriques sous forme de journaux
Une fois AllMetrics activé dans les paramètres de diagnostic de votre instance de Standard Load Balancer, si vous utilisez un Event Hub ou un espace de travail Log Analytics, ces journaux sont renseignés dans la table AzureMonitor. En cas d’exportation vers le stockage, connectez-vous à votre compte de stockage et récupérez les entrées de journal JSON pour les journaux des événements et des sondes d’intégrité. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, Power BI ou tout autre outil de visualisation de données. 

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.

## <a name="stream-to-an-event-hub"></a>Diffuser vers un hub d’événements
Quand les informations de diagnostic sont envoyées en streaming vers un hub d’événements, elles peuvent être utilisées pour une analyse centralisée des journaux dans un outil SIEM tiers avec intégration à Azure Monitor. Pour plus d’informations, consultez [Envoyer en streaming des données de supervision Azure vers un hub d’événements](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Envoyer à Log Analytics
Les informations de diagnostic des ressources dans Azure peuvent être envoyées directement à un espace de travail Log Analytics où des requêtes complexes peuvent être exécutées sur les informations en vue d’un dépannage et d’une analyse.  Pour plus d’informations, consultez [Collecter les journaux de ressources Azure dans l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Limites
Actuellement, les limitations suivantes s’appliquent à l’utilisation de la fonctionnalité d’exportation des métriques sous forme de journaux pour les instances de Load Balancer :
* Les métriques qui s’affichent utilisent des noms internes lorsqu’elles sont exportées sous forme de journaux. Le mappage correspondant est disponible dans le tableau ci-dessous.
* La dimensionnalité des métriques n’est pas conservée. Par exemple, avec des métriques telles que DipAvailability (état de la sonde d’intégrité), vous ne pouvez pas fractionner ou afficher par adresse IP de serveur principal
* Actuellement, les ports SNAT utilisés et les métriques de ports SNAT alloués ne sont pas disponibles pour l’exportation sous forme de journaux

## <a name="next-steps"></a>Étapes suivantes
* [Examiner les métriques pour votre instance de Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Créer et tester des requêtes en suivant les instructions Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Fournir des commentaires sur cet article ou sur les fonctionnalités de Load Balancer à l’aide des liens ci-dessous
