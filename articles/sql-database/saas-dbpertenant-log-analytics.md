---
title: Utiliser des journaux Azure Monitor avec une application mutualisée SQL Database | Microsoft Docs
description: Configurer et utiliser des journaux Azure Monitor avec une application SaaS Azure SQL Database multilocataire
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6380488faa9a4554df5df5ea67e11dbeb8853fff
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455919"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurer et utiliser des journaux Azure Monitor avec une application SaaS SQL Database multilocataire

Dans ce didacticiel, vous configurez et utilisez [Azure Monitor enregistre](/azure/log-analytics/log-analytics-overview) pour surveiller les pools élastiques et bases de données. Ce didacticiel s’appuie sur le [didacticiel Surveiller et gérer les performances des bases de données SQL Azure et des pools dans une application SaaS multilocataire](saas-dbpertenant-performance-monitoring.md). Il montre comment utiliser les journaux d’Azure Monitor pour améliorer la surveillance et création d’alertes disponibles dans le portail Azure. Azure Monitor enregistre prend en charge des milliers de pools élastiques et des centaines de milliers de bases de données de surveillance. Journaux d’Azure Monitor fournit une solution de surveillance unique qui permettre intégrer la surveillance de différentes applications et services Azure dans plusieurs abonnements Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer les journaux d’Azure Monitor.
> * Utilisez les journaux Azure Monitor pour surveiller les pools et bases de données.

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* L’application de base de données par locataire SaaS Wingtip Tickets est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez [Déployer et explorer une application multi-locataire SaaS qui illustre le modèle de base de données par locataire avec Azure SQL Database](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Pour plus d’informations sur les scénarios et les modèles SaaS, et leur incidence sur les exigences applicables à une solution de supervision, consultez le [didacticiel Surveiller et gérer les performances des bases de données SQL Azure et des pools dans une application SaaS multilocataire](saas-dbpertenant-performance-monitoring.md).

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Surveiller et gérer les performances de pool élastique et base de données avec les journaux d’Azure Monitor

Pour Azure SQL Database, la surveillance et les alertes sont disponibles pour les bases de données et les pools dans le portail Azure. Ces fonctions de surveillance et de génération d’alertes intégrées sont pratiques, mais également propres à la ressource. Cela signifie qu’elles sont moins bien adaptées pour surveiller les installations de grande taille ou fournir une vue unifiée sur l’ensemble des ressources et abonnements.

Pour les scénarios à volumes élevés, vous pouvez utiliser les journaux Azure Monitor pour la surveillance et génération d’alertes. Azure Monitor est un service Azure distinct qui permet l’analytique sur les journaux de diagnostic et les données de télémétrie recueillies dans un espace de travail à partir de nombreux services. Journaux d’Azure Monitor offre un requête intégré langage et des outils qui permettent l’analytique des données opérationnelles. La solution SQL Analytics fournit plusieurs vues et requêtes prédéfinies de surveillance et d’alertes pour les pools élastiques et les bases de données. Journaux d’Azure Monitor fournit également un concepteur de vues personnalisées.

Les espaces de travail OMS sont désormais appelés « espaces de travail Log Analytics ». Les espaces de travail et les solutions d’analyse de Log Analytics s’ouvrent dans le Portail Azure. Le Portail Azure constitue le point d’accès le plus récent, mais il peut être moins évolué que le portail Operations Management Suite dans certains domaines.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Créer des données de diagnostic des performances en simulant une charge de travail sur vos locataires 

1. Dans PowerShell ISE, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Gardez ce script ouvert, car vous souhaiterez peut-être exécuter plusieurs des scénarios de génération de charge au cours de ce didacticiel.
1. Si ce n’est déjà fait, approvisionnez un lot de locataires pour bénéficier d’un contexte de surveillance plus intéressant. Ce processus prend quelques minutes.

   a. Définissez **$DemoScenario = 1**, _Approvisionner un lot de locataires_.

   b. Pour exécuter le script et déployer 17 locataires supplémentaires, appuyez sur F5.

1. Démarrez le Générateur de charge pour exécuter une charge simulée sur tous les locataires.

    a. Définissez **$DemoScenario = 2**, _générer une charge d’intensité normale (environ 30 DTU)_.

    b. Pour exécuter le script, appuyez sur la touche F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l’application de base de données par locataire SaaS Wingtip Tickets

Les scripts et le code de l’application de base de données mutualisée SaaS Wingtip Tickets sont disponibles dans le répertoire GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets PowerShell.

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installer et configurer l’espace de travail Analytique de journal et de la solution d’Analytique de SQL Azure

Azure Monitor est un service distinct qui doit être configuré. Azure Monitor enregistre collecte les données de journal, les données de télémétrie et les mesures dans un espace de travail Analytique de journal. Tout comme les autres ressources dans Azure, un espace de travail Log Analytics doit être créé. L’espace de travail ne doit pas être créé dans le même groupe de ressources que les applications qu’il surveille, même si cela est souvent l’option la plus logique. Pour l’application Wingtip Tickets, utilisez un groupe de ressources unique pour vous assurer que l’espace de travail est supprimé avec l’application.

1. Dans PowerShell ISE, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Pour exécuter le script, appuyez sur la touche F5.

Maintenant que vous pouvez ouvrir Azure Monitor enregistre dans le portail Azure. Il faut compter quelques minutes pour que les données de télémétrie soient collectées dans l’espace de travail Log Analytics et deviennent visibles. Plus vous laisserez de temps au système pour rassembler des données de diagnostic, plus l’expérience sera intéressante. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utiliser l’espace de travail Analytique de journal et de la solution d’Analytique de SQL pour surveiller les pools et bases de données


Dans cet exercice, ouvrez l’espace de travail Analytique de journal dans le portail Azure pour examiner les données de télémétrie collectées pour les bases de données et les pools.

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **tous les services** pour ouvrir l’espace de travail Analytique de journal. Recherchez ensuite Log Analytics.

   ![Espace de travail Analytique de journal ouvert](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Sélectionnez l’espace de travail nommé _wtploganalytics-&lt;user&gt;_.

1. Sélectionnez **vue d’ensemble** pour ouvrir la solution d’analytique de journal dans le portail Azure.

   ![Présentation](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L’activation de la solution peut prendre quelques minutes. 

1. Sélectionnez la vignette **Azure SQL Analytics** pour l’ouvrir.

    ![Vignette d’aperçu](media/saas-dbpertenant-log-analytics/overview.png)

1. Vous pouvez faire défiler horizontalement les vues dans la solution à l’aide de leur propre barre de défilement située en bas. Actualisez la page, si nécessaire.

1. Pour explorer la page de résumé, sélectionnez les vignettes ou les bases de données individuelles pour ouvrir un explorateur détaillé.

    ![Tableau de bord analytique de journal](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Modifiez le paramètre de filtre pour changer l’intervalle de temps. Pour ce didacticiel, choisissez **Dernière heure**.

    ![Filtre de temps](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Sélectionnez une base de données individuelle pour explorer l’utilisation des requêtes et les métriques pour cette base de données.

    ![Analyse de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Pour afficher l’utilisation des métriques, faites défiler la page de l’analyse vers la droite.
 
     ![Métriques de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Faites défiler la page de l’analyse vers la gauche, puis sélectionnez la vignette du serveur dans la liste **Informations sur les ressources**.  

    ![Liste Informations sur les ressources](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Une page présentant les pools et les bases de données sur le serveur s’ouvre.

    ![Serveur avec pools et bases de données](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Sélectionnez un pool. Sur la page du pool qui s’ouvre, faites défiler vers la droite pour afficher les métriques du pool. 

    ![Métriques du pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Revenez dans l’espace de travail Log Analytics, sélectionnez **Portail OMS** pour ouvrir l’espace de travail à cet endroit.

    ![Espace de travail Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Dans l’espace de travail Log Analytics, vous pouvez explorer plus en détail les données de journal d’activité et les métriques. 

Surveillance et les alertes dans Azure Monitor journaux sont basées sur des requêtes sur les données dans l’espace de travail, contrairement aux alertes définies sur chaque ressource dans le portail Azure. En basant les alertes sur les requêtes, vous pouvez définir une seule alerte qui examine toutes les bases de données plutôt que d’en définir une par base de données. Les requêtes sont seulement limitées par les données disponibles dans l’espace de travail.

Pour plus d’informations sur l’utilisation des journaux Azure Monitor pour interroger et définir des alertes, consultez [fonctionnent avec les règles d’alerte dans Azure Monitor enregistre](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Journaux d’analyse Azure pour SQL Database est facturée en fonction du volume de données dans l’espace de travail. Dans ce didacticiel, vous avez créé un espace de travail gratuit, qui est limité à 500 Mo par jour. Une fois cette limite atteinte, l’ajout de données à l’espace de travail est interrompu.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer les journaux d’Azure Monitor.
> * Utilisez les journaux Azure Monitor pour surveiller les pools et bases de données.

Essayez maintenant le [didacticiel sur l’analyse des locataires](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement initial de l’application de base de données Wingtip Tickets SaaS par locataire](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Journaux Azure Monitor](../azure-monitor/insights/azure-sql.md)
