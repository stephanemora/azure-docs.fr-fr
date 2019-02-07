---
title: Gestion et mise à jour des machines virtuelles avec Azure Stack | Microsoft Docs
description: Découvrez comment utiliser les solutions Update Management, Change Tracking et Inventory dans Azure Automation pour gérer les machines virtuelles Windows et Linux déployées dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 69a0bde58a365ff124bc41b6039c606cb38f71fe
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768543"
---
# <a name="azure-stack-vm-update-and-management"></a>Gestion et mise à jour des machines virtuelles avec Azure Stack
Vous pouvez utiliser les fonctionnalités des solutions Azure Automation suivantes pour gérer les machines virtuelles Windows et Linux déployées avec Azure Stack :

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)**. La solution Update Management vous permet d’évaluer rapidement l’état des mises à jour disponibles sur tous les ordinateurs d’agent et de gérer le processus d’installation des mises à jour obligatoires pour ces machines virtuelles Windows et Linux.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Les modifications apportées aux logiciels installés, aux services Windows, aux fichiers et Registre Windows et aux démons Linux sur les serveurs surveillés sont envoyées au service cloud Log Analytics pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

- **[Inventory](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Le suivi Inventory pour une machine virtuelle dans Azure Stack fournit une interface utilisateur basée sur le navigateur pour définir et configurer la collecte d’inventaire. 

> [!IMPORTANT]
> Ces solutions sont les mêmes que celles utilisées pour gérer des machines virtuelles Azure. Les machines virtuelles Azure et Azure Stack sont gérées de la même façon, à partir de la même interface et avec les mêmes outils. Les machines virtuelles Azure Stack sont également au même prix que les machines virtuelles Azure lors de l’utilisation des solutions Update Management, Change Tracking et Inventory avec Azure Stack.

## <a name="prerequisites"></a>Prérequis
Plusieurs prérequis doivent être satisfaits avant de pouvoir utiliser ces fonctionnalités pour mettre à jour et gérer des machines virtuelles Azure Stack. Ces prérequis incluent les étapes qui doivent être suivies dans le portail Azure, ainsi que le portail d’administration Azure Stack.

### <a name="in-the-azure-portal"></a>Dans le portail Azure
Pour utiliser les fonctionnalités Azure Automation Inventory, Change Tracking et Update Management pour les machines virtuelles Azure Stack, vous devez d’abord activer ces solutions dans Azure.

> [!TIP]
> Si ces fonctionnalités sont déjà activées pour les machines virtuelles Azure, vous pouvez utiliser vos informations d’identification d’espace de travail Log Analytics préexistantes. Si vous avez déjà une clé primaire et un ID d’espace de travail Log Analytics que vous souhaitez utiliser, passez directement à [la section suivante](./vm-update-management.md#in-the-azure-stack-administration-portal). Sinon, poursuivez dans cette section pour créer un espace de travail Log Analytics et un compte Automation.

La première étape de l’activation de ces solutions consiste à [créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) dans votre abonnement Azure. Un espace de travail Log Analytics est un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Une fois que vous avez créé un espace de travail, notez la clé et l’ID. Pour afficher ces informations, accédez au panneau de l’espace de travail, cliquez sur **Paramètres avancés** et passez en revue les valeurs pour **ID de l’espace de travail** et **Clé primaire**. 

Vous devez ensuite [créer un compte Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Un compte Automation est un conteneur pour vos ressources Azure Automation. Il fournit un moyen de séparer vos environnements ou de mieux organiser vos flux de travail et ressources Automation. Une fois que le compte Automation est créé, vous devez activer les fonctionnalités Inventory, Change Tracking et Update Management. Pour ce faire, effectuez les étapes suivantes pour activer chaque fonctionnalité :

1. Dans le portail Azure, accédez au compte Automation que vous souhaitez utiliser.

2. Sélectionnez la solution à activer (**Inventory**, **Change Tracking**, ou **Update Management**).

3. Utilisez la liste déroulante **Sélectionner un espace de travail...** pour sélectionner l’espace de travail Log Analytics à utiliser.

4. Vérifiez que toutes les informations restantes sont correctes, puis cliquez sur **Activer** pour activer la solution.

5. Répétez les étapes 2 à 4 pour activer l’ensemble des trois solutions. 

   [![](media/vm-update-management/1-sm.PNG "Activer les fonctionnalités de compte Automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>Dans le portail d’administration Azure Stack
Après avoir activé les solutions Azure Automation dans le portail Azure, vous devez vous connecter au portail d’administration Azure Stack en tant qu’administrateur cloud, puis télécharger les éléments d’extension **Azure Update and Configuration Management** et **Azure Update and Configuration Management for Linux** de la Place de marché Azure Stack. 

   ![Extension Azure Update and Configuration Management de la Place de marché](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Activer Update Management pour les machines virtuelles Azure Stack
Effectuez les étapes suivantes pour activer la gestion des mises à jour pour les machines virtuelles Azure Stack.

1. Connectez-vous au portail utilisateur Azure Stack.

2. Dans le portail utilisateur Azure Stack, accédez au panneau Extensions des machines virtuelles pour lesquelles vous voulez activer ces solutions, cliquez sur **+ Ajouter**, sélectionnez l’extension **Azure Update and Configuration Management**, puis cliquez sur **Créer** :

   [![](media/vm-update-management/3-sm.PNG "Panneau Extension de machine virtuelle")](media/vm-update-management/3-lg.PNG#lightbox)

3. Indiquez les clé primaire et ID d’espace de travail précédemment créés pour lier l’agent à l’espace de travail Log Analytics et cliquez sur **OK** pour déployer l’extension.

   [![](media/vm-update-management/4-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Comme décrit dans la [documentation sur la gestion des mises à jour Automation](https://docs.microsoft.com/azure/automation/automation-update-management), vous devez activer la solution Update Management pour chaque machine virtuelle que vous souhaitez gérer. Pour activer la solution pour toutes les machines virtuelles associées à l’espace de travail, sélectionnez **Gestion des mises à jour**, cliquez sur **Gérer des machines**, puis sélectionnez l’option **Activer sur tous les ordinateurs disponibles et à venir**.

   [![](media/vm-update-management/5-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Répétez cette étape pour activer chaque solution pour les machines virtuelles Azure Stack qui sont associées à l’espace de travail. 
  
Une fois que l’extension Azure Update and Configuration Management est activée, une analyse est effectuée deux fois par jour pour chaque machine virtuelle gérée. L’API est appelée toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si l’état a changé, une analyse de conformité est lancée.

Une fois que les machines virtuelles sont analysées, elles s’affichent dans le compte Azure Automation de la solution Update Management : 

   [![](media/vm-update-management/6-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> L’affichage sur le tableau de bord des données mises à jour provenant des ordinateurs gérés peut prendre entre 30 minutes et 6 heures.

Les machines virtuelles Azure Stack peuvent maintenant être incluses dans des déploiements de mise à jour planifiés avec des machines virtuelles Azure.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Activer Update Management à l’aide d’un modèle Resource Manager
Si vous avez un grand nombre de machines virtuelles Azure Stack, vous pouvez utiliser [ce modèle Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) pour déployer plus facilement la solution sur des machines virtuelles. Le modèle déploie l’extension Microsoft Monitoring Agent sur une machine virtuelle Azure Stack existante et l’ajoute à un espace de travail Azure Log Analytics existant.
 
## <a name="next-steps"></a>Étapes suivantes
[Optimiser les performances de SQL Server](azure-stack-sql-server-vm-considerations.md)
