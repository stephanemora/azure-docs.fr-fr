---
title: Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure
description: Cet article fournit une vue d’ensemble de l’utilisation d’Azure Automation - Gestion des mises à jour pour gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054757"
---
# <a name="manage-windows-updates-with-azure-automation"></a>Gérer les mises à jour Windows avec Azure Automation

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles.
Dans ce tutoriel, vous allez découvrir comment évaluer rapidement l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires, passer en revue les résultats des déploiements et créer une alerte pour vérifier que les mises à jour sont appliquées correctement.

Pour plus d’informations sur les prix, consultez [Tarification Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Configurer les alertes
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas encore, vous pouvez [activer votre crédit Azure mensuel pour abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire pour obtenir un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche d’observateur.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-update-management"></a>Activer la gestion des mises à jour

Pour ce tutoriel, vous devez d’abord activer la gestion des mises à jour sur votre machine virtuelle.

1. Dans le portail Azure, dans le menu de gauche, sélectionnez **Machines virtuelles**, puis choisissez une machine virtuelle dans la liste.
2. Dans la page de la machine virtuelle, cliquez sur **Gestion des mises à jour** sous la section **Opérations**. La page **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle. Cette validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et détermine si la solution de gestion des mises à jour se trouve dans l’espace de travail.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour. L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un runbook Worker hybride Automation.
Cet agent est utilisé pour communiquer avec Azure Automation et obtenir des informations sur l’état des mises à jour. L’agent nécessite que le port 443 soit ouvert pour communiquer avec le service Azure Automation et télécharger des mises à jour.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Compte Automation](./automation-offering-get-started.md)
* Un [worker runbook hybride](./automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Gestion des mises à jour** s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

![Fenêtre Activer la solution de gestion des mises à jour](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L’activation de la solution peut prendre quelques minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur.
Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées à Log Analytics.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît.
Si des mises à jour sont manquantes, vous pouvez afficher une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

Sélectionnez le **LIEN INFORMATIONS** sur la mise à jour pour ouvrir l’article de la mise à jour dans une nouvelle fenêtre. Vous y trouverez des informations importantes sur la mise à jour.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Cliquez n’importe où sur la mise à jour pour ouvrir la fenêtre **Recherche dans les journaux** pour la mise à jour sélectionnée. La requête pour la recherche dans les journaux est prédéfinie pour cette mise à jour particulière. Vous pouvez modifier cette requête ou créer votre propre requête afin d’afficher des informations détaillées sur les mises à jour déployées ou manquantes dans votre environnement.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Configurer les alertes

Pendant cette étape, vous configurez une alerte pour être tenu informé des mises à jour qui ont été correctement déployées. L’alerte que vous créez repose sur une requête Log Analytics. Vous pouvez écrire des requêtes personnalisées pour que d’autres alertes couvrent d’autres scénarios. Dans le portail Azure, accédez à **Surveiller** et cliquez sur **Créer une alerte**. La page **Créer une règle** s’ouvre.

Sous **1. Définir la condition de l’alerte**, cliquez sur **+ Sélectionner la cible**. Sous **Filtrer par type de ressource**, sélectionnez **Log Analytics**. Choisissez votre espace de travail Log Analytics et cliquez sur **Terminé**.

![Créer une alerte](./media/automation-tutorial-update-management/create-alert.png)

Cliquez sur le bouton **+ Ajouter des critères** pour ouvrir la page **Configurer la logique du signal**. Choisissez **Recherche dans les journaux personnalisée** dans le tableau. Entrez la requête suivante dans la zone de texte **Requête de recherche**. Cette requête retourne le nom des ordinateurs et de l’exécution de mise à jour qui s’est déroulée dans le délai d’exécution spécifié.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Entrez **1** comme **seuil** de la logique d’alerte. Lorsque vous avez terminé, cliquez sur **Terminé**.

![Configurer la logique du signal](./media/automation-tutorial-update-management/signal-logic.png)

Sous **2. Définir les détails de l’alerte**, donnez à l’alerte un nom convivial et une description. Affectez à l’option **Gravité** la valeur **Pour informations (gravité 2)** étant donné que l’alerte a trait à une exécution réussie.

![Configurer la logique du signal](./media/automation-tutorial-update-management/define-alert-details.png)

Sous **3. Définir un groupe d’actions**, cliquez sur **+ Nouveau groupe d’actions**. Un groupe d’actions est un groupe que vous pouvez utiliser dans plusieurs alertes. Il peut s’agir, sans s’y limiter, de notifications par e-mail, de runbooks, de webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md).

Dans la zone **Nom du groupe d’actions**, définissez un nom convivial et un nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

Sous **Actions**, l’action porte un nom convivial comme **Notifications par e-mail**. Sous **TYPE D’ACTION**, sélectionnez **E-mail/SMS/Push/Voix**. Sous **DÉTAILS**, sélectionnez **Modifier les détails**.

Dans la page **E-mail/SMS/Push/Voix**, attribuez un nom. Cochez la case **E-mail** et entrez une adresse e-mail valide à utiliser.

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/configure-email-action-group.png)

Cliquez sur **OK** dans la page **E-mail/SMS/Push/Voix** pour la fermer, puis cliquez sur **OK** pour fermer la page **Ajouter un groupe d’actions**.

Vous pouvez personnaliser l’objet de l’e-mail envoyé en cliquant sur **Objet de l’e-mail** sous **Personnaliser les actions** dans la page **Créer une règle**. Lorsque vous avez terminé, cliquez sur **Créer une règle d’alerte**. Ainsi est créée la règle qui vous avertit quand un déploiement de mises à jour s’est correctement déroulé et précise quelles machines en ont bénéficié.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Maintenant que les alertes sont configurées, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions pour installer des mises à jour.
Vous pouvez choisir les types de mises à jour à inclure dans le déploiement.
Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

> [!WARNING]
> Lorsque les mises à jour nécessitent un redémarrage, la machine virtuelle est automatiquement redémarrée.

Planifiez un nouveau déploiement de mises à jour pour la machine virtuelle en revenant à **Gestion des mises à jour** et en sélectionnant **Planifier le déploiement de la mise à jour** en haut de l’écran.

Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour le déploiement de mises à jour.

* **Système d’exploitation** : choisissez le système d’exploitation à cibler pour le déploiement de mises à jour.

* **Classification de mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Pour ce didacticiel, conservez tous les types sélectionnés.

  Les types de classification sont les suivants :

   |SE  |type  |
   |---------|---------|
   |Windows     | Mises à jour critiques</br>Mises à jour de sécurité</br>Correctifs cumulatifs</br>Packs de fonctionnalités</br>Service Packs</br>Mises à jour de définitions</br>Outils</br>Mises à jour        |
   |Linux     | Mises à jour critiques et de sécurité</br>Autres mises à jour       |

   Pour obtenir la description des types de classification, consultez [Classifications des mises à jour](automation-update-management.md#update-classifications).

* **Paramètres de planification** : permet d’ouvrir la page Paramètres de planification. L’heure de début par défaut est dans 30 minutes. Vous pouvez lui affecter la valeur 10 minutes à l’avenir.

   Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique.
   Sélectionnez **Une fois** sous **Récurrence**. Conservez la valeur par défaut de 1 jour, cliquez sur **OK**. Cela configure une planification récurrente.

* **Fenêtre de maintenance (en minutes)** : conservez cette valeur par défaut. Vous pouvez spécifier la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Ce paramètre permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

![Écran Paramètres de planification des mises à jour](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer**. Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
L’état est **En cours d’exécution** lorsqu’il est en cours d’exécution.
Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
En cas d’échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.
Cliquez sur le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Dans la vignette **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur la machine virtuelle.
Le tableau de droite montre une répartition détaillée de chaque mise à jour et les résultats de l’installation.
La liste suivante présente les valeurs disponibles :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussi** : la mise à jour a réussi
* **Échec** : la mise à jour a échoué

Cliquez sur **Tous les journaux** pour voir toutes les entrées de journal créées par le déploiement.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Cliquez sur **Erreurs** pour afficher les informations détaillées sur les erreurs du déploiement.

Une fois que votre déploiement de mises à jour a réussi, un e-mail similaire à l’illustration suivante est envoyé pour en témoigner.

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Configurer les alertes
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

Passez à la vue d’ensemble de la solution de gestion des mises à jour.

> [!div class="nextstepaction"]
> [Solution de gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
