---
title: Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure
description: Cet article fournit une vue d’ensemble de l’utilisation de la gestion des mises à jour d’Azure Automation pour gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 6046781f59b64dcec4769686a2acd710c7b68965
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987305"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Gérer les mises à jour Windows à l’aide d’Azure Automation

Vous pouvez utiliser la solution de gestion des mises à jour pour gérer les mises à jour et les correctifs pour vos machines virtuelles. Dans ce tutoriel, vous allez découvrir comment évaluer rapidement l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires, passer en revue les résultats des déploiements et créer une alerte pour vérifier que les mises à jour sont appliquées correctement.

Pour plus d’informations sur les prix, consultez [Tarification d’Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Configurer les alertes
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas encore, vous pouvez [activer votre crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire pour obtenir un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Azure Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche Watcher.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-update-management"></a>Activer Update Management

Pour ce tutoriel, vous devez d’abord activer la gestion des mises à jour sur votre machine virtuelle :

1. Dans le menu de gauche du portail Azure, sélectionnez **Machines virtuelles**. Sélectionnez une machine virtuelle dans la liste.
2. Sur la page de la machine virtuelle, sous **OPÉRATIONS**, sélectionnez **Gestion des mises à jour**. Le volet **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle. Cette validation inclut la vérification de l’existence d’un espace de travail Azure Log Analytics et d’un compte Automation lié, et détermine si la solution de gestion des mises à jour se trouve dans l’espace de travail.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour. L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un runbook Worker hybride Automation. Cet agent est utilisé pour communiquer avec Azure Automation et obtenir des informations sur l’état des mises à jour. L’agent nécessite que le port 443 soit ouvert pour communiquer avec le service Azure Automation et télécharger des mises à jour.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* Un [compte Automation](./automation-offering-get-started.md)
* Un [runbook Worker hybride](./automation-hybrid-runbook-worker.md) (activé sur la machine virtuelle)

Sous **Gestion des mises à jour**, définissez l’emplacement, l’espace de travail Log Analytics et le compte Automation à utiliser. Ensuite, sélectionnez **Activer**. Si ces options ne sont pas disponibles, cela signifie qu’une autre solution Automation est activée pour la machine virtuelle. Dans ce cas, les mêmes espace de travail et compte Automation doivent être utilisés.

![Fenêtre Activer la solution de gestion des mises à jour](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L’activation de la solution peut prendre quelques minutes. Pendant ce temps, ne fermez pas la fenêtre du navigateur. Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées à Log Analytics. Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la gestion des mises à jour activée, le volet **Gestion des mises à jour** s’ouvre. Si des mises à jour sont manquantes, une liste des mises à jour manquantes s’affiche sous l’onglet **Mises à jour manquantes**.

Sous le **LIEN D'INFORMATIONS**, sélectionnez le lien de mise à jour pour ouvrir l’article du support de la mise à jour dans une nouvelle fenêtre. Vous y trouverez des informations importantes sur la mise à jour.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Cliquez n’importe où sur la mise à jour pour ouvrir le volet **Recherche dans les journaux** pour la mise à jour sélectionnée. La requête pour la recherche dans les journaux est prédéfinie pour cette mise à jour spécifique. Vous pouvez modifier cette requête ou créer votre propre requête afin d’afficher des informations détaillées sur les mises à jour déployées ou manquantes dans votre environnement.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurer des alertes

Dans cette étape, vous allez apprendre à configurer une alerte pour être tenu informé des mises à jour qui ont été correctement déployées via une requête Log Analytics ou grâce au suivi du runbook principal pour la gestion des mises à jour des déploiements qui ont échoué.

### <a name="alert-conditions"></a>Conditions d’alerte

Pour chaque type d’alerte, il existe différentes conditions d’alerte qui doivent être définies.

#### <a name="log-analytics-query-alert"></a>Alerte de requête Log Analytics

Vous pouvez créer une alerte basée sur une requête Log Analytics pour les déploiements réussis. Pour les déploiements ayant échoué, vous pouvez utiliser les étapes de l’[Alerte de runbook](#runbook-alert) pour être averti lorsque le runbook principal qui orchestre les déploiements de mises à jour échoue. Vous pouvez écrire une requête personnalisée pour que d’autres alertes couvrent d’autres scénarios.

Dans le portail Azure, accédez à **Surveiller**, puis sélectionnez **Créer une alerte**.

Sous **1. Définir la condition de l’alerte**, cliquez sur **Sélectionner la cible**. Sous **Filtrer par type de ressource**, sélectionnez **Log Analytics**. Sélectionnez votre espace de travail Log Analytics, puis sélectionnez **Terminé**.

![Créer une alerte](./media/automation-tutorial-update-management/create-alert.png)

Sélectionnez **Ajouter des critères**.

Sous **Configurer la logique du signal**, dans le tableau, sélectionnez **Recherche de journal personnalisée**. Entrez la requête suivante dans la zone de texte **Requête de recherche** :

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
Cette requête retourne le nom des ordinateurs et de l’exécution de mise à jour qui s’est déroulée dans le délai d’exécution spécifié.

Sous **Logique d’alerte**, pour **Seuil**, entrez **1**. Quand vous avez terminé, cliquez sur **Terminé**.

![Configurer la logique du signal](./media/automation-tutorial-update-management/signal-logic.png)

#### <a name="runbook-alert"></a>Alerte de runbook

Pour les déploiements ayant échoué, vous devez signaler l’échec du runbook maître.
Dans le portail Azure, accédez à **Surveiller**, puis sélectionnez **Créer une alerte**.

Sous **1. Définir la condition de l’alerte**, cliquez sur **Sélectionner la cible**. Sous **Filtrer par type de ressource**, sélectionnez **Comptes Automation**. Sélectionnez votre compte Automation, puis **Terminé**.

Pour **Nom du runbook**, cliquez sur le signe **\+** et entrez **Patch-MicrosoftOMSComputers** comme nom personnalisé. Pour **État**, choisissez **Échec** ou cliquez sur le signe **\+** pour entrer **Échec**.

![Configurer la logique du signal pour des runbooks](./media/automation-tutorial-update-management/signal-logic-runbook.png)

Sous **Logique d’alerte**, pour **Seuil**, entrez **1**. Quand vous avez terminé, cliquez sur **Terminé**.

### <a name="alert-details"></a>Détails de l’alerte

Sous **2. Définissez les détails de l’alerte**, entrez un nom et une description pour l’alerte. Définissez **Gravité** sur **Informations (gravité 2)** pour une exécution réussie, ou sur **Informations (gravité 1)** pour une exécution ayant échoué.

![Configurer la logique du signal](./media/automation-tutorial-update-management/define-alert-details.png)

Sous **3. Définissez un groupe d’actions**, sélectionnez **Nouveau groupe d’actions**. Un groupe d’actions est un groupe que vous pouvez utiliser dans plusieurs alertes. Les actions peuvent inclure, sans s’y limiter, les notifications par e-mail, les runbooks, les webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md).

Dans la zone **Nom du groupe d’actions** , entrez un nom pour l’alerte et un nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

Sous **Actions**, entrez un nom pour l’action, tel que **Notifications par e-mail**. Sous **TYPE D’ACTION**, sélectionnez **E-mail/SMS/Push/Voix**. Sous **DÉTAILS**, sélectionnez **Modifier les détails**.

Dans le volet **E-mail/SMS/Push/Voix**, entrez un nom. Sélectionnez la case à cocher **E-mail**, puis entrez une adresse e-mail valide.

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/configure-email-action-group.png)

Dans le volet **E-mail/SMS/Push/Voix**, sélectionnez **OK**. Dans le volet **Ajouter un groupe d’actions**, sélectionnez **OK**.

Pour personnaliser l’objet de l’e-mail d’alerte, sous **Créer une règle**, sous **Personnaliser les actions**, sélectionnez **Objet de l’e-mail**. Lorsque vous avez terminé, cliquez sur **Créer une règle d’alerte**. L’alerte vous avertit quand un déploiement de mises à jour s’est correctement déroulé et précise quelles machines en ont bénéficié.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Ensuite, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions pour installer les mises à jour. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

Pour planifier un nouveau déploiement de mises à jour pour la machine virtuelle, accédez à **Gestion des mises à jour**, puis cliquez sur **Planifier le déploiement de la mise à jour**.

Sous **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : entrez un nom unique pour le déploiement de mises à jour.

* **Système d’exploitation** : sélectionnez le système d’exploitation à cibler pour le déploiement de mises à jour.

* **Groupes à mettre à jour (préversion)** : définissez une requête basée sur une combinaison de l’abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour plus d’informations, consultez [Groupes dynamiques](automation-update-management.md#using-dynamic-groups)

* **Machines to update** (Ordinateurs à mettre à jour) : sélectionnez une recherche enregistrée, un groupe importé ou choisissez un ordinateur dans la liste déroulante, puis sélectionnez des ordinateurs individuels. Si vous choisissez **Machines**, l’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans Log Analytics, consultez [Groupes d’ordinateurs dans Log Analytics](../log-analytics/log-analytics-computer-groups.md)

* **Classification de mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Pour ce didacticiel, conservez tous les types sélectionnés.

  Les types de classification sont les suivants :

   |SE  |type  |
   |---------|---------|
   |Windows     | Mises à jour critiques</br>Mises à jour de sécurité</br>Correctifs cumulatifs</br>Packs de fonctionnalités</br>Service Packs</br>Mises à jour de définitions</br>Outils</br>Mises à jour        |
   |Linux     | Mises à jour critiques et de sécurité</br>Autres mises à jour       |

   Pour obtenir la description des types de classification, consultez [Classifications des mises à jour](automation-update-management.md#update-classifications).

* **Mises à jour à inclure/exclure** : ceci ouvre la page **Inclure/Exclure**. Les mises à jour à inclure ou à exclure sont sous des onglets distincts. Pour plus d’informations sur la façon dont l’inclusion est gérée, consultez [Comportement d’inclusion](automation-update-management.md#inclusion-behavior)

* **Paramètres de planification** : le volet **Paramètres de planification** s’ouvre. L’heure de début par défaut est dans 30 minutes. Vous pouvez définir l’heure de début à tout moment à partir de 10 minutes à l’avenir.

   Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Sous **Récurrence**, sélectionnez **Une fois**. Laissez la valeur par défaut sur 1 jour et sélectionnez **OK**. Cela configure une planification récurrente.

* **Pré-scripts + post-scripts** : sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer les pré-scripts et les post-scripts](pre-post-scripts.md).
* **Fenêtre de maintenance (en minutes)** : conservez la valeur par défaut. Vous pouvez définir la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Ce paramètre permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

* **Reboot options** (Options de redémarrage) : ce paramètre détermine comment les redémarrages doivent être traités. Options disponibles :
  * Redémarrer si nécessaire (par défaut)
  * Toujours redémarrer
  * Ne jamais redémarrer
  * Redémarrer uniquement : les mises à jour ne sont pas installées

Lorsque vous avez terminé de configurer la planification, sélectionnez **Créer**.

![Volet Paramètres de planification des mises à jour](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

> [!NOTE]
> Update Management prend en charge le déploiement des mises à jour principales et le téléchargement préalable des correctifs. Cela nécessite des modifications sur les systèmes en cours de correction, consultez [first party and pre-download support](automation-update-management.md#firstparty-predownload) (prise en charge principale et téléchargement préalable) pour apprendre à configurer ces paramètres sur vos systèmes.

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** sous **Gestion des mises à jour**. Si le déploiement est en cours d’exécution, son état est **En cours**. Une fois le déploiement terminé, s’il a réussi, l’état passe à **Réussi**. En cas d’échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.

Sélectionnez le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Sous **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur la machine virtuelle. Le tableau de droite montre une répartition détaillée de chaque mise à jour et les résultats de l’installation.

La liste suivante présente les valeurs disponibles :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussi** : la mise à jour a réussi.
* **Échec** : la mise à jour a échoué.

Pour afficher toutes les entrées de journal créées par le déploiement, sélectionnez **Tous les journaux**.

Cliquez sur **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

Une fois que votre déploiement de mises à jour a réussi, un e-mail similaire à l’exemple suivant est envoyé pour en témoigner :

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour la gestion des mises à jour
> * Afficher une évaluation des mises à jour
> * Configurer les alertes
> * Planifier un déploiement de mises à jour
> * Afficher les résultats d’un déploiement

Passez à la vue d’ensemble de la solution de gestion des mises à jour.

> [!div class="nextstepaction"]
> [Solution de gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
