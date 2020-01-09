---
title: Détecter les logiciels installés sur vos machines avec Azure Automation | Microsoft Docs
description: Utilisez l’inventaire pour connaître les logiciels installés sur les machines dans votre environnement.
services: automation
keywords: inventaire, automatisation, suivi des modifications
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 136521799dbc928a03c339ecc1cef6fdd3d029b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420575"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Détecter les logiciels installés sur vos machines Azure et non-Azure

Dans ce didacticiel, vous allez apprendre à détecter les logiciels installés dans votre environnement. Vous pouvez collecter et afficher l’inventaire des logiciels, fichiers, démons Linux, services Windows et des clés de registre Windows présents sur vos ordinateurs. Le suivi des configurations de vos machines peut vous aider à identifier les problèmes opérationnels au sein de votre environnement et à mieux comprendre l’état de vos machines.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer la solution
> * Intégrer une machine virtuelle Azure
> * Intégrer une machine virtuelle non-Azure
> * Afficher les logiciels installés
> * Rechercher les journaux d’activité d’inventaire des logiciels installés

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche Watcher.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Activer le suivi des modifications et l’inventaire

Pour ce didacticiel, vous devez d’abord activer le suivi des modifications et l’inventaire. Si vous avez déjà activé la solution **Change Tracking**, cette étape n’est pas nécessaire.

Accédez à votre compte Automation et sélectionnez **Inventory** sous **GESTION DE LA CONFIGURATION**.

Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

![Bannière de configuration intégrée d’inventaire](./media/automation-tutorial-installed-software/enableinventory.png)

Pour activer la solution, configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme l’inventaire.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

L’activation de la solution peut prendre jusqu’à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur.
Une fois la solution activée, des informations sur les logiciels installés et les changements apportés à la machine virtuelle sont envoyées aux journaux d’activité Azure Monitor.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="onboard-a-vm"></a>Intégrer une machine virtuelle

Dans votre compte Automation, accédez à **Inventory** sous **GESTION DE LA CONFIGURATION**.

Sélectionnez **+ Add Azure VM** (+ Ajouter une machine virtuelle Azure), la page **Machines virtuelles** s’ouvre et vous permet de sélectionner une machine virtuelle existante à dans la liste. Sélectionnez la machine virtuelle que vous souhaitez intégrer. Sur la page qui s’ouvre, cliquez sur **activer** pour activer la solution sur la machine virtuelle. L’agent de gestion Microsoft est déployé sur la machine virtuelle et configure l’agent pour communiquer avec l’espace de travail Log Analytics que vous avez configuré lors de l’activation de la solution. L’exécution de l’intégration peut prendre plusieurs minutes. À ce stade, vous pouvez sélectionner une nouvelle machine virtuelle dans la liste et en intégrer une autre.

## <a name="onboard-a-non-azure-machine"></a>Intégrer une machine non-Azure

Pour ajouter des machines non-Azure, installez l’agent pour [Windows](../azure-monitor/platform/agent-windows.md) ou [Linux](automation-linux-hrw-install.md) selon votre système d’exploitation. Une fois l’agent installé, accédez à votre compte Automation et à **Inventory** sous **GESTION DE LA CONFIGURATION**. Lorsque vous cliquez sur **Manage Machines** (Gérer les machines), une liste des machines signalant à votre espace de travail Log Analytics que la solution n’y est pas activée s’affiche. Sélectionnez l’option appropriée pour votre environnement.

* **Enable on all available machines** (Activer sur toutes les machines disponibles) : cette option active la solution sur toutes les machines communiquant avec votre espace de travail Log Analytics à ce moment.
* **Enable on all available machines and future machines** (Activer sur toutes les machines disponibles et les machines futures) : cette option active la solution sur toutes les machines communiquant avec votre espace de travail Log Analytics, ainsi que sur toutes les machines ajoutées à l’avenir à l’espace de travail.
* **Enable on selected machines** (Activer sur les machines sélectionnées) : cette option active la solution sur les machines que vous avez sélectionnées uniquement.

![Gérer les machines](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Afficher les logiciels installés

Lorsque la solution de suivi des modifications et d’inventaire est activée, vous pouvez voir les résultats sur la page **Inventaire**.

Dans votre compte Automation, sélectionnez **Inventory** sous **GESTION DE LA CONFIGURATION**.

Sur la page **Inventaire**, cliquez sur l’onglet **Logiciels**.

Dans l’onglet **Logiciels**, un tableau dresse la liste des logiciels détectés. Les logiciels sont regroupés par nom et par version.

Les informations détaillées sur chaque enregistrement de logiciel sont visibles dans le tableau. Ces détails comprennent le nom du logiciel, la version, l’éditeur, l’heure de la dernière actualisation (heure de l’actualisation la plus récente signalée par une machine du groupe) et les machines (nombre de machines possédant ce logiciel).

![Inventaires des logiciels](./media/automation-tutorial-installed-software/inventory-software.png)

Cliquez sur une ligne pour afficher les propriétés de l’enregistrement du logiciel et le nom des machines qui le possèdent.

Pour rechercher un logiciel ou un groupe de logiciels spécifique, vous pouvez faire une recherche dans la zone de texte située juste au-dessus de la liste des logiciels.
Le filtre vous permet de faire une recherche à partir du nom du logiciel, de la version ou de l’éditeur.

Par exemple, la recherche de « Contoso » renvoie tous les logiciels dont le nom, l’éditeur ou la version contient « Contoso ».

## <a name="search-inventory-logs-for-installed-software"></a>Rechercher les journaux d’activité d’inventaire des logiciels installés

L’inventaire génère des données de journal d’activité qui sont envoyées aux journaux d’activité Azure Monitor. Pour rechercher les journaux d’activité en exécutant des requêtes, sélectionnez **Log Analytics** en haut de la fenêtre **Inventaire**.

Les données d’inventaire sont stockées sous le type **ConfigurationData**.
L’exemple de requête Log Analytics suivant retourne les résultats d’inventaire dans lesquels le serveur de publication est « Microsoft Corporation ».

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Pour en savoir plus sur l’exécution et la recherche de fichiers journaux dans les journaux d’activité Azure Monitor, consultez [Journaux d’activité Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="single-machine-inventory"></a>Inventaire d’une machine unique

Pour voir l’inventaire des logiciels d’une seule machine, vous pouvez accéder à l’inventaire à partir de la page de ressources de la machine virtuelle Azure ou utiliser les journaux d’activité Azure Monitor pour filtrer la machine correspondante.
L’exemple suivant de requête Log Analytics retourne la liste des logiciels pour une machine nommée ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a appris à afficher l’inventaire des logiciels et notamment comment :

> [!div class="checklist"]
> * Activer la solution
> * Intégrer une machine virtuelle Azure
> * Intégrer une machine virtuelle non-Azure
> * Afficher les logiciels installés
> * Rechercher les journaux d’activité d’inventaire des logiciels installés

Accédez ensuite à la vue d’ensemble de la solution de suivi des modifications et d’inventaire pour en savoir plus.

> [!div class="nextstepaction"]
> [Solution de gestion des changements et d’inventaire](automation-change-tracking.md)
