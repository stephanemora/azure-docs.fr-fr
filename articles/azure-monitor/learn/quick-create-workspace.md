---
title: Créer un espace de travail Log Analytics dans le portail Azure | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud dans le portail Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0ab8019a8537d4080afdb457459f09cd156ff4ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365527"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Créer un espace de travail Log Analytics dans le portail Azure
Utilisez le menu **Espaces de travail Log Analytics** pour créer un espace de travail Log Analytics à l’aide du portail Azure. Un espace de travail Log Analytics est un environnement unique pour les données de journal d’activité Azure Monitor. Chaque espace de travail dispose d’un référentiel de données et d’une configuration propres. Les sources de données et les solutions sont configurées de façon à stocker leurs données dans un espace de travail particulier. Vous devez avoir un espace de travail Log Analytics si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement
* Ordinateurs locaux surveillés par System Center Operations Manager
* Collections d’appareils de System Center Configuration Manager 
* Données de diagnostics ou de journaux du Stockage Azure

Pour les autres sources, telles que les machines virtuelles Azure et les machines virtuelles Windows ou Linux dans votre environnement, consultez les rubriques suivantes :

*  [Collect data from Azure virtual machines](../learn/quick-collect-azurevm.md) (Collecter des données à partir de machines virtuelles Azure) 
*  [Collect data from hybrid Linux computer](../learn/quick-collect-linux-computer.md) (Collecter des données à partir d’un ordinateur Linux hybride)
*  [Collect data from hybrid Windows computer](quick-collect-windows-computer.md) (Collecter des données à partir d’un ordinateur Windows hybride)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

    ![Portail Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Cliquez sur **Ajouter**, puis sélectionnez les options pour les éléments suivants :

   * Attribuez un nom au nouvel **Espace de travail Log Analytics** comme *DefaultLAWorkspace*. Ce nom doit être globalement unique sur l’ensemble des abonnements Azure Monitor.
   * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   * Pour **Groupe de ressources**, choisissez d’utiliser un groupe de ressources déjà configuré ou créez-en un.  
   * Sélectionnez un **Emplacement** disponible.  Pour plus d’informations, consultez les [régions dans lesquelles Log Analytics est disponible](https://azure.microsoft.com/regions/services/) et recherchez Azure Monitor à partir du champ **Rechercher un produit**.  
   * Si vous créez un espace de travail dans un nouvel abonnement créé après le 2 avril 2018, il utilisera automatiquement le plan tarifaire *Par Go* et l’option pour sélectionner un niveau de tarification ne sera pas disponible.  Si vous créez un espace de travail pour un abonnement existant créé avant le 2 avril, ou pour un abonnement lié à une inscription à un Contrat Entreprise existante, sélectionnez le niveau tarifaire de votre choix.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Créer le panneau de ressources Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Après avoir entré les informations requises dans le volet **Espace de travail Log Analytics**, cliquez sur **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires. 

* Pour activer la collecte de données à partir de ressources Azure avec Diagnostics Azure ou le stockage Azure, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Ajoutez [System Center Operations Manager comme source de données](../platform/om-agents.md) pour collecter des données à partir des agents rattachés à votre groupe d’administration Operations Manager et les stocker dans votre espace de travail Log Analytics. 
* Connectez [Configuration Manager](../platform/collect-sccm.md) pour importer des ordinateurs qui sont membres de collections dans la hiérarchie.  
* Examinez les [solutions de surveillance](../insights/solutions.md) disponibles et la façon d’ajouter ou de supprimer une solution dans votre espace de travail.
