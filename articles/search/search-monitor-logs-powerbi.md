---
title: Visualiser les journaux et les métriques Recherche cognitive Azure avec Power BI
description: Visualiser les journaux et les métriques Recherche cognitive Azure avec Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650142"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualiser les journaux et les métriques Recherche cognitive Azure avec Power BI
[Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search) vous permet de stocker les journaux des opérations et les métriques de service relatifs à votre service de recherche dans un compte de stockage Azure. Cette page fournit des instructions sur la façon dont vous pouvez visualiser ces informations par le biais d’une application modèle Power BI. L’application fournit des informations détaillées sur votre service de recherche, notamment des informations sur les métriques Recherche, Indexation, Opérations et Service.

L’application modèle Power BI **Recherche cognitive Azure : Analyser les journaux et les métriques** est accessible dans le [marketplace d’applications Power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Prise en main de l’application
1. Activez la journalisation des diagnostics pour votre service de recherche :
    1. Créez ou identifiez un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) existant dans lequel vous pouvez archiver les journaux.
    1. Accédez à votre service Recherche cognitive Azure dans le Portail Azure.
    1. Dans la section Supervision de la colonne gauche, sélectionnez **Paramètres de diagnostic**.

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Sélectionnez **+ Ajouter un paramètre de diagnostic**.
    1. Cochez la case **Archiver dans un compte de stockage**, fournissez les informations de votre compte de stockage, puis cochez les cases **OperationLogs** et **AllMetrics**.

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Sélectionnez **Enregistrer**.

1. Une fois la journalisation activée, utilisez votre service de recherche pour commencer à générer des journaux d’activité et des mesures. Cela prend jusqu’à une heure avant que les conteneurs n’apparaissent dans le Stockage Blob avec ces journaux. Vous verrez un conteneur **insights-logs-operationlogs** pour les journaux de trafic de recherche et un conteneur **insights-metrics-pt1m** pour les métriques.

1. Recherchez l’application Recherche cognitive Azure Power BI dans le [marketplace d’applications Power BI](https://appsource.microsoft.com/marketplace/apps) et installez-la dans un nouvel espace de travail ou un espace de travail existant. L’application s’appelle **Recherche cognitive Azure : Analyser les journaux et les métriques**.

1. Après avoir installé l’application, sélectionnez-la à partir de votre liste d’applications dans Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Sélectionnez **Se connecter** pour connecter vos données.

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Entrez le nom du compte de stockage qui contient vos journaux et métriques. Par défaut, l’application examine les 10 derniers jours de données, mais cette valeur peut être modifiée avec le paramètre **Jours**.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Sélectionnez **Clé** comme méthode d’authentification et indiquez votre clé de compte de stockage. Sélectionnez **Privé** comme niveau de confidentialité. Cliquez sur Se connecter pour commencer le processus de chargement.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Attendez que les données soient actualisées. Cela peut prendre un certain temps en fonction de la quantité de données dont vous disposez. Vous pouvez voir si les données sont toujours en cours d’actualisation d’après l’indicateur ci-dessous.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Une fois l’actualisation des données terminée, sélectionnez **Rapport Recherche cognitive Azure** pour afficher le rapport.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Veillez à actualiser la page avant d’avoir ouvert le rapport afin qu’il s’ouvre avec vos données.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Comment modifier les paramètres de l’application
Si vous souhaitez visualiser des données à partir d’un autre compte de stockage ou modifier le nombre de jours de données à interroger, suivez les étapes ci-dessous pour modifier les paramètres **Jours** et **Compte de stockage**.

1. Accédez à vos applications Power BI, recherchez votre application Recherche cognitive Azure et sélectionnez le bouton **Modifier l’application** pour afficher l’espace de travail.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Sélectionnez **Paramètres** parmi les options du jeu de données.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Dans l’onglet Jeux de données, modifiez les valeurs des paramètres et sélectionnez **Appliquer**. En cas de problème avec la connexion, mettez à jour les informations d’identification de la source de données sur la même page.

1. Revenez à l’espace de travail et sélectionnez **Actualiser** parmi les options du jeu de données.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Ouvrez le rapport pour afficher les données mises à jour. Vous devrez peut-être actualiser également le rapport pour afficher les données les plus récentes.

## <a name="troubleshooting"></a>Dépannage
Si vous constatez que vous ne pouvez pas voir vos données, suivez ces étapes de résolution de problèmes :

1. Ouvrez le rapport et actualisez la page pour vous assurer de disposer des données les plus récentes. Une option du rapport permet d’actualiser les données. Sélectionnez-la pour récupérer les données les plus récentes.

1. Vérifiez que le nom du compte de stockage et la clé d’accès que vous avez fournis sont corrects. Le nom du compte de stockage doit correspondre au compte configuré avec les journaux de votre service de recherche.

1. Vérifiez que votre compte de stockage contient les conteneurs **insights-logs-operationlogs** et **insights-metrics-pt1m** et que chaque conteneur contient des données. Les journaux et les métriques se trouvent dans deux ou trois couches de dossiers.

1. Vérifiez si le jeu de données est toujours en cours d’actualisation. L’indicateur d’état d’actualisation est indiqué à l’étape 8 ci-dessus. S’il est toujours en cours d’actualisation, patientez jusqu’à la fin de l’actualisation pour ouvrir et actualiser le rapport.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Recherche cognitive Azure](https://docs.microsoft.com/azure/search/)

[Qu’est-ce que Power BI ?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Concepts de base pour les concepteurs du service Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)