---
title: Gérer les mises à jour pour plusieurs machines virtuelles Azure
description: Cette rubrique décrit la gestion des mises à jour pour les machines virtuelles Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 658686bec41fe1a6cfa8ca4ba6fe61d2e559297c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833717"
---
# <a name="manage-updates-for-multiple-machines"></a>Gérer les mises à jour pour plusieurs ordinateurs

Vous pouvez utiliser la gestion des mises à jour pour gérer les mises à jour et les correctifs pour vos machines virtuelles Windows et Linux. À partir de votre compte [Azure Automation](automation-offering-get-started.md) compte, vous pouvez :

- Intégrer des machines virtuelles.
- Évaluer l’état des mises à jour disponibles.
- Planifier l’installation des mises à jour requises.
- Passer en revue les résultats du déploiement pour vérifier que les mises à jour ont été appliquées correctement à toutes les machines virtuelles pour lesquelles la gestion des mises à jour est activée.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des mises à jour, vous devez avoir :

- Un compte d’identification Azure Automation. Pour savoir comment en créer un, consultez [Prise en main d'Azure Automation](automation-offering-get-started.md).

- Une machine virtuelle ou un ordinateur virtuel avec l’un des systèmes d’exploitation pris en charge.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

La gestion des mises à jour est prise en charge par les systèmes d’exploitation suivants :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures     |Windows PowerShell 4.0 ou une version ultérieure sont requis ([télécharger WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)).</br> Windows PowerShell 5.1 ([télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) est recommandé pour accroître la fiabilité.         |
|CentOS 6 (x86/x64) et 7 (x 64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Red Hat Enterprise 6 (x86/x64) et 7 (x 64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)      |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

> [!NOTE]
> Pour empêcher que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations, consultez la [rubrique Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Les agents Linux doivent avoir accès à un référentiel de mise à jour.

Cette solution ne prend pas en charge d’Agent Operations Management Suite pour Linux configuré pour envoyer des rapports à plusieurs espaces de travail Log Analytics.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Activer la gestion des mises à jour pour les machines virtuelles Azure

Dans le portail Azure, ouvrez votre compte Automation; et sélectionnez **Gestion des mises à jour**.

En haut de la fenêtre, sélectionnez **Ajouter une machine virtuelle Azure**.

![Ajouter une machine virtuelle Azure](./media/manage-update-multi/update-onboard-vm.png)

Sélectionnez une machine virtuelle à intégrer. La boîte de dialogue **Activer la gestion des mises à jour** apparaît. Sélectionnez **Activer** pour intégrer la machine virtuelle. Une fois que l’intégration est terminée, la gestion des mises à jour est activée pour votre machine virtuelle.

![Boîte de dialogue Activer la gestion des mises à jour](./media/manage-update-multi/update-enable.png)

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Activer la gestion des mises à jour pour les machines et ordinateurs virtuels non Azure

Pour obtenir des instructions sur l’activation de la gestion de la mise à jour pour les machines et ordinateurs virtuels Windows non Azure, consultez [Connecter des ordinateurs Windows au service Log Analytics dans Azure](../log-analytics/log-analytics-windows-agent.md).

Pour obtenir des instructions sur l’activation de la gestion de la mise à jour pour les machines et ordinateurs virtuels Linux non Azure, consultez [Connecter vos ordinateurs Linux à Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Afficher les ordinateurs attachés à votre compte Automation

Après avoir activé la gestion des mises à jour pour vos machines, vous pouvez afficher leurs informations en cliquant sur **Ordinateurs**. Les informations d’ordinateur telles que *Nom*, *Conformité*, *Environnement*, *Type de système d’exploitation*, *Mises à jour critiques et de sécurité*, *Autres mises à jour* et *Préparation à la mise à jour de l’agent* sont disponibles.

  ![Afficher l’onglet des ordinateurs](./media/manage-update-multi/update-computers-tab.png)

Les ordinateurs pour lesquels la gestion des mises à jour a été activée n’ont peut-être pas encore été évalués. L’état de conformité de ces ordinateurs serait *Non évalué*.  Voici une liste des valeurs de l’état de conformité :

- Conforme : ordinateurs avec aucune mise à jour critique ou de sécurité manquante.

- Non-conforme : ordinateurs avec des mises à jour critiques ou de sécurité manquantes.

- Non évalué : l’ordinateur n’a transmis aucune donnée d’évaluation de mise à jour dans le délai escompté.  Ce délai est de trois heures pour les ordinateurs Linux et de douze heures pour les ordinateurs Windows.

Pour afficher l’état de l’agent, cliquez sur le lien dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT** colonne. Cela ouvre la page du worker hybride qui indique l’état du worker hybride. L’illustration suivante montre un exemple d’agent qui n’a pas été connecté à la gestion des mises à jour pendant une période prolongée.

![Afficher l’onglet des ordinateurs](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Afficher une évaluation des mises à jour

Une fois la gestion des mises à jour activée, la boîte de dialogue **Gestion des mises à jour** apparaît. Vous pouvez voir une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

## <a name="collect-data"></a>Collecter les données

Les agents installés sur des machines et ordinateurs virtuels collectent des données sur les mises à jour et les envoient à la gestion de la mise à jour Azure.

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées prises en charge par cette solution :

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows |OUI |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents et lance l’installation des mises à jour obligatoires. |
| Agents Linux |OUI |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |OUI |La gestion de la mise à jour collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté. |
| Compte Azure Storage |Non  |Le stockage Azure n’inclut aucune information sur les mises à jour du système. |

### <a name="collection-frequency"></a>Fréquence de collecte

Pour chaque ordinateur Windows géré, une analyse est effectuée deux fois par jour. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si c’est le cas, une analyse de conformité est effectuée. Pour chaque ordinateur Linux géré, une analyse est effectuée toutes les 3 heures.

L’affichage des données mises à jour des ordinateurs gérés dans le tableau de bord peut prendre de 30 minutes à 6 heures.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions.
Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

Planifiez un nouveau déploiement de mises à jour pour une ou plusieurs machines virtuelles en cliquant sur **Planifier le déploiement de la mise à jour** en haut de la boîte de dialogue **Gestion des mises à jour**.
Dans le volet **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

- **Nom** : spécifiez un nom unique pour identifier le déploiement de mises à jour.
- **Type de système d’exploitation** : sélectionnez Windows ou Linux.
- **Machines à mettre à jour** : sélectionnez les machines virtuelles que vous souhaitez mettre à jour. L’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT** colonne. Cela permet d’afficher l’état d’intégrité de la machine avant de planifier le déploiement des mises à jour.

  ![Volet Nouveau déploiement de mises à jour](./media/manage-update-multi/update-select-computers.png)

- **Classification de la mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour inclura. Pour obtenir la description des types de classification, consultez [Classifications des mises à jour](automation-update-management.md#update-classifications). Les types de classification sont les suivants :
  - Mises à jour critiques
  - Mises à jour de sécurité
  - Correctifs cumulatifs
  - Packs de fonctionnalités
  - Service Packs
  - Mises à jour de définitions
  - Outils
  - Mises à jour

- **Mises à jour à exclure** : cette action ouvre la page **Exclure**. Entrez les bases de connaissances ou les noms des packages à exclure.

- **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut, qui est de 30 minutes après l’heure actuelle. Ou bien vous pouvez spécifier une heure différente.
   Vous pouvez également spécifier si le déploiement se produit une seule fois ou selon une planification périodique. Pour définir une planification périodique, sélectionnez l’option **Récurrent** sous **Périodicité**.

   ![Boîte de dialogue Paramètres de planification](./media/manage-update-multi/update-set-schedule.png)

- **Fenêtre de maintenance (en minutes)** : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Ce paramètre permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

Une fois que vous avez terminé la configuration de la planification, revenez au tableau de bord des états en cliquant sur le bouton **Créer**. Le tableau **Planifié** indique la planification de déploiement que vous avez créée.

> [!WARNING]
> Pour les mises à jour nécessitant un redémarrage, la machine virtuelle redémarre automatiquement.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans la boîte de dialogue **Gestion des mises à jour**.
Si le déploiement est en cours d’exécution, son état est **En cours**. Une fois le déploiement terminé, son état devient **Réussi**.
En cas d’échec d’une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.

![État du déploiement des mises à jour](./media/manage-update-multi/update-view-results.png)

Pour voir le tableau de bord dédié au déploiement des mises à jour, sélectionnez le déploiement terminé.

Le volet **Résultats des mises à jour** affiche un récapitulatif du nombre total de mises à jour et les résultats du déploiement sur la machine virtuelle.
Dans le tableau de droite se trouve une répartition détaillée de chaque mise à jour et les résultats de l’installation. Les résultats de l’installation peuvent être l’une des valeurs suivantes :

- Aucune tentative effectuée : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée de fenêtre de maintenance définie.
- Réussi : la mise à jour a réussi.
- Échec : la mise à jour a échoué.

Pour afficher toutes les entrées de journal créées par le déploiement, sélectionnez **Tous les journaux**.

Pour voir le flux des tâches du runbook qui gère le déploiement des mises à jour sur la machine virtuelle cible, sélectionnez la vignette **Sortie**.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la gestion des mises à jour (y compris les journaux, la sortie et les erreurs), consultez [Solution Update Management dans Azure](../operations-management-suite/oms-solution-update-management.md).
