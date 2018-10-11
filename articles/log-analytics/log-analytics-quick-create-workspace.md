---
title: Créer un espace de travail Log Analytics dans le portail Azure | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud dans le portail Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 08/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: edebeec493b025a81a99c0458344aafe59e769e9
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040872"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Créer un espace de travail Log Analytics dans le portail Azure
Dans le Portail Azure, vous pouvez configurer un espace de travail Log Analytics, environnement Log Analytics unique doté de ses propres référentiel de données, sources de données et solutions.  Vous devez suivre les étapes décrites dans cet article si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement
* Ordinateurs locaux surveillés par System Center Operations Manager
* Collections d’appareils de System Center Configuration Manager 
* Données de diagnostics ou de journaux du Stockage Azure

Pour les autres sources, telles que les machines virtuelles Azure et les machines virtuelles Windows ou Linux dans votre environnement, consultez les rubriques suivantes :

*  [Collect data from Azure virtual machines](log-analytics-quick-collect-azurevm.md) (Collecter des données à partir de machines virtuelles Azure) 
*  [Collect data from hybrid Linux computer](log-analytics-quick-collect-linux-computer.md) (Collecter des données à partir d’un ordinateur Linux hybride)
*  [Collect data from hybrid Windows computer](log-analytics-quick-collect-windows-computer.md) (Collecter des données à partir d’un ordinateur Windows hybride)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.

    ![Portail Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)
  
2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

  * Attribuez un nom au nouvel **Espace de travail Log Analytics**, tel que *DefaultLAWorkspace*. 
  * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
  * Pour **Groupe de ressources**, choisissez d’utiliser un groupe de ressources déjà configuré ou créez-en un.  
  * Sélectionnez un **Emplacement** disponible.  Pour plus d’informations, découvrez dans quelles [régions Log Analytics est disponible](https://azure.microsoft.com/regions/services/).
  * Si vous créez un espace de travail dans un nouvel abonnement créé après le 2 avril 2018, il utilisera automatiquement le plan tarifaire *Par Go* et l’option pour sélectionner un niveau de tarification ne sera pas disponible.  Si vous créez un espace de travail pour un abonnement existant créé avant le 2 avril, ou pour un abonnement lié à une inscription à un Contrat Entreprise existante, sélectionnez le niveau tarifaire de votre choix.  Pour plus d’informations sur les différents niveaux proposés, consultez le [détail des tarifs de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)  

3. Après avoir entré les informations requises dans le volet **Espace de travail Log Analytics**, cliquez sur **OK**.  

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu. 

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires. 

* Pour activer la collecte de données à partir de ressources Azure avec Azure Diagnostics ou le stockage Azure, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](log-analytics-azure-storage.md).  
* Ajoutez [System Center Operations Manager comme source de données](log-analytics-om-agents.md) pour collecter des données à partir des agents rattachés à votre groupe d’administration Operations Manager et les stocker dans votre espace de travail Log Analytics. 
* Connectez [Configuration Manager](log-analytics-sccm.md) pour importer des ordinateurs qui sont membres de collections dans la hiérarchie.  
* Examinez les [solutions de gestion](https://docs.microsoft.com/azure/monitoring/monitoring-solutions-inventory?toc=%2fazure%2flog-analytics%2ftoc.json) disponibles et la façon d’ajouter ou de supprimer une solution dans votre espace de travail.
