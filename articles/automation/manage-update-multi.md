---
title: Gérer les mises à jour pour plusieurs machines virtuelles Azure
description: Cet article décrit la gestion des mises à jour pour les machines virtuelles Azure et non-Azure.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: e9a5a4330a90bd376114f836250e290944f03860
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417823"
---
# <a name="manage-updates-for-multiple-machines"></a>Gérer les mises à jour pour plusieurs ordinateurs

Vous pouvez utiliser la solution de gestion des mises à jour pour gérer les mises à jour et les correctifs pour vos machines virtuelles Windows et Linux. À partir de votre compte [Azure Automation](automation-offering-get-started.md) compte, vous pouvez :

- Intégrer des machines virtuelles
- Évaluer l’état des mises à jour disponibles
- Planifier l’installation des mises à jour requises
- Passez en revue les résultats du déploiement pour vérifier que les mises à jour ont été appliquées correctement à toutes les machines virtuelles pour lesquelles la gestion des mises à jour est activée

## <a name="prerequisites"></a>Conditions préalables requises

Pour utiliser la gestion des mises à jour, vous devez avoir :

- Une machine virtuelle ou un ordinateur virtuel avec l’un des systèmes d’exploitation pris en charge.

- Un accès à un référentiel de mises à jour pour les machines virtuelles Linux intégrées à la solution.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

La gestion des mises à jour est prise en charge par les systèmes d’exploitation suivants :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures     |Windows PowerShell 4.0 ou une version ultérieure est nécessaire. ([Télécharger WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 est recommandé pour accroître la fiabilité. ([Télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) et 7 (x64)      | |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 LTS (x86/x64)      | |

> [!NOTE]
> Pour empêcher que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations, consultez la [rubrique Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

## <a name="enable-update-management-for-azure-virtual-machines"></a>Activer la gestion des mises à jour pour les machines virtuelles Azure

Dans le portail Azure, ouvrez votre compte Automation, puis sélectionnez **Gestion des mises à jour**.

Sélectionnez **Ajouter des machines virtuelles Azure**.

![Ajouter une machine virtuelle Azure](./media/manage-update-multi/update-onboard-vm.png)

Sélectionnez une machine virtuelle à intégrer.

Sous **Activer la gestion des mises à jour**, sélectionnez **Activer** pour intégrer la machine virtuelle.

![Boîte de dialogue Activer la gestion des mises à jour](./media/manage-update-multi/update-enable.png)

Une fois que l’intégration est terminée, la gestion des mises à jour est activée pour votre machine virtuelle.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Activer la gestion des mises à jour pour les machines et ordinateurs virtuels non Azure

Vous devez installer l’agent Log Analytics pour Windows et Linux sur les machines virtuelles qui s’exécutent sur votre réseau d’entreprise ou dans un autre environnement cloud afin de les activer avec Update Management. Pour connaître la configuration système requise et les méthodes prises en charge pour déployer l’agent sur les machines hébergées en dehors d’Azure, consultez [Vue d’ensemble de l’agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Afficher les ordinateurs attachés à votre compte Automation

Après avoir activé la gestion des mises à jour pour vos machines, vous pouvez afficher leurs informations en cliquant sur **Ordinateurs**. Vous pouvez afficher des informations sur le *nom de la machine*, l’*état de conformité*, l’*environnement*, le *type de système d’exploitation*, les *mises à jour critiques et de sécurité installées*, les *autres mises à jour installées*, et la *préparation à la mise à jour de l’agent* pour vos ordinateurs.

  ![Afficher l’onglet des ordinateurs](./media/manage-update-multi/update-computers-tab.png)

Les ordinateurs pour lesquels la gestion des mises à jour a été récemment activée n’ont peut-être pas encore été évalués. L’état de conformité de ces ordinateurs est **Non évalué**. Voici une liste des valeurs possibles de l’état de conformité :

- **Conforme** : ordinateurs sans aucune mise à jour critique ou de sécurité manquante.

- **Non conforme** : ordinateurs avec des mises à jour critiques ou de sécurité manquantes.

- **Non évalué** : l’ordinateur n’a transmis aucune donnée d’évaluation de mise à jour dans le délai prévu. Pour les ordinateurs Linux, le délai prévu est au cours de la dernière heure. Pour les ordinateurs Windows, le délai prévu est au cours des 12 dernières heures.

Pour afficher l’état de l’agent, cliquez sur le lien dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**. Cette action ouvre le volet **Worker hybride** qui indique l’état du worker hybride. L’illustration suivante montre un exemple d’agent qui n’a pas été connecté à la gestion des mises à jour pendant une période prolongée :

![Afficher l’onglet des ordinateurs](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Afficher une évaluation des mises à jour

Une fois la gestion des mises à jour activée, le volet **Gestion des mises à jour** s’ouvre. Vous pouvez voir une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

## <a name="collect-data"></a>Collecter les données

Les agents installés sur des machines et ordinateurs virtuels collectent des données sur les mises à jour. Les agents envoient les données à la gestion des mises à jour Azure.

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées prises en charge par cette solution :

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows |Oui |La gestion des mises à jour collecte des informations sur les mises à jour système des agents Windows et lance l’installation des mises à jour obligatoires. |
| Agents Linux |Oui |La gestion des mises à jour collecte des informations sur les mises à jour système des agents Linux et lance l’installation des mises à jour obligatoires sur les distributions prises en charge. |
| Groupe d’administration d’Operations Manager |Oui |La gestion des mises à jour collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté. |
| Compte de Stockage Azure |Non |Le stockage Azure n’inclut aucune information sur les mises à jour système. |

### <a name="collection-frequency"></a>Fréquence de collecte

Après qu’une machine a terminé l’analyse de conformité de la mise à jour, l’agent transfère les informations en bloc aux journaux Azure Monitor. Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut.

En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si MMA est redémarré, avant et après l’installation de la mise à jour.

Sur un ordinateur Linux, l’analyse de conformité est effectuée toutes les heures par défaut. Si l’agent MMA est redémarré, une analyse de conformité est lancée dans les 15 minutes.

L’affichage sur le tableau de bord des données mises à jour provenant des ordinateurs gérés peut prendre entre 30 minutes et 6 heures.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui s’aligne avec votre fenêtre de planification et de maintenance des versions. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

>[!NOTE]
>Quand vous planifiez un déploiement de mises à jour, cela entraîne la création d’une ressource de [planification](shared-resources/schedules.md) liée au runbook **Patch-MicrosoftOMSComputers**, qui gère le déploiement des mises à jour sur les machines cibles. Si vous supprimez la ressource de planification du portail Azure, ou si vous utilisez PowerShell après la création du déploiement, le déploiement de mises à jour planifié est interrompu. De plus, une erreur est générée quand vous essayez de le reconfigurer à partir du portail. Vous ne pouvez supprimer la ressource de planification qu’en supprimant la planification de déploiement correspondante.
>

Pour planifier un nouveau déploiement de mises à jour pour une ou plusieurs machines virtuelles, sous **Gestion des mises à jour**, sélectionnez **Planifier le déploiement de mises à jour**.

Dans le volet **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

- **Name** : entrez un nom unique pour identifier le déploiement de mises à jour.
- **Système d’exploitation** : sélectionnez **Windows** ou **Linux**.
- **Groupes à mettre à jour** : Définissez une requête basée sur une combinaison de l’abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour les machines virtuelles non-Azure, les recherches enregistrées sont utilisées pour créer un groupe dynamique à inclure dans votre déploiement. Pour plus d’informations, consultez [Groupes dynamiques](automation-update-management-groups.md).
- **Machines à mettre à jour** : sélectionnez Recherche enregistrée, Groupe importé ou Machines pour choisir les machines que vous souhaitez mettre à jour.

   >[!NOTE]
   >La sélection de l’option Recherche enregistrée ne retourne pas les identités de machine, mais uniquement leur nom. Si vous avez plusieurs machines virtuelles avec le même nom dans plusieurs groupes de ressources, celles-ci sont retournées dans les résultats. Il est recommandé d’utiliser l’option **Groupes à mettre à jour** pour veiller à inclure les machines virtuelles uniques correspondant à vos critères.

   Si vous choisissez **Machines**, l’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**. Vous pouvez afficher l’état d’intégrité de la machine avant de planifier le déploiement des mises à jour. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../azure-monitor/platform/computer-groups.md).

  ![Volet Nouveau déploiement de mises à jour](./media/manage-update-multi/update-select-computers.png)

- **Classification des mises à jour** : sélectionnez les types de logiciels à inclure dans le déploiement de mises à jour. Pour obtenir la description des types de classification, consultez [Classifications des mises à jour](automation-view-update-assessments.md#update-classifications). Les types de classification sont les suivants :
  - Mises à jour critiques
  - Mises à jour de sécurité
  - Correctifs cumulatifs
  - Packs de fonctionnalités
  - Service Packs
  - Mises à jour de définitions
  - Outils
  - Mises à jour

- **Mises à jour à inclure/exclure** : ceci ouvre la page **Inclure/Exclure**. Les mises à jour à inclure ou à exclure sont sous des onglets distincts. Pour plus d’informations sur la façon dont l’inclusion est gérée, consultez [Planifier un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut ; cette valeur est de 30 minutes après l’heure actuelle. Vous pouvez également spécifier une heure différente.

   Vous pouvez également spécifier si le déploiement se produit une seule fois ou selon une planification périodique. Pour définir une planification périodique, sous **Périodicité**, sélectionnez **Récurrent**.

   ![Boîte de dialogue Paramètres de planification](./media/manage-update-multi/update-set-schedule.png)

- **Préscripts + postscripts** : sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer les pré-scripts et les post-scripts](pre-post-scripts.md).
- **Fenêtre de maintenance (minutes)**  : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Ce paramètre permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

- **Contrôle de redémarrage** : ce paramètre détermine la gestion des redémarrages pour le déploiement des mises à jour.

   |Option|Description|
   |---|---|
   |Redémarrer si nécessaire| **(Valeur par défaut)** Si nécessaire, un redémarrage est effectué si la fenêtre de maintenance le permet.|
   |Toujours redémarrer|Un redémarrage est initié, qu’il soit demandé ou non. |
   |Ne jamais redémarrer|Les redémarrages sont empêchés, qu’ils soient nécessaires ou non.|
   |Redémarrer uniquement : les mises à jour ne sont pas installées|Cette option ignore l’installation des mises à jour et initie uniquement un redémarrage.|

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer** pour revenir au tableau de bord des états. Le tableau **Planifié** indique la planification de déploiement que vous avez créée.

> [!NOTE]
> Update Management prend en charge le déploiement des mises à jour principales et le téléchargement préalable des correctifs. Cela nécessite des modifications sur les systèmes en cours de correction, consultez [first party and predownload support](automation-configure-windows-update.md#pre-download-updates) (prise en charge principale et téléchargement préalable) pour apprendre à configurer ces paramètres sur vos systèmes.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** sous **Gestion des mises à jour**.

Si le déploiement est en cours d’exécution, son état est **En cours**. Une fois le déploiement terminé, son état devient **Réussi**.

En cas d’échec d’une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.

![État du déploiement des mises à jour](./media/manage-update-multi/update-view-results.png)

Pour voir le tableau de bord dédié au déploiement des mises à jour, sélectionnez le déploiement terminé.

Le volet **Résultats des mises à jour** affiche un récapitulatif du nombre total de mises à jour et les résultats du déploiement de la machine virtuelle. Dans le tableau de droite se trouvent une répartition détaillée de chaque mise à jour et les résultats de l’installation. Les résultats de l’installation peuvent être l’une des valeurs suivantes :

- **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée de fenêtre de maintenance définie.
- **Opération réussie** : la mise à jour a réussi.
- **Échec** : la mise à jour a échoué.

Pour afficher toutes les entrées de journal créées par le déploiement, sélectionnez **Tous les journaux d’activité**.

Pour voir le flux des tâches du runbook qui gère le déploiement des mises à jour sur la machine virtuelle cible, sélectionnez la vignette Sortie.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion des mises à jour, y compris les journaux d’activité, la sortie et les erreurs, consultez [Solution de gestion de mises à jour dans Azure](../operations-management-suite/oms-solution-update-management.md).

