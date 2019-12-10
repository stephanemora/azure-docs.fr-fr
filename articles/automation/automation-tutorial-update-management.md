---
title: Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure
description: Cet article fournit une vue d’ensemble de l’utilisation d’Azure Automation Update Management afin de gérer les mises à jour et les correctifs pour vos machines virtuelles Azure et non-Azure.
services: automation
author: mgoedtel
ms.service: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 12/03/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 44372f32227bbfef46a72afa6f9b0bd88b29905b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806556"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure

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

1. Dans le menu [Portail Azure](https://portal.azure.com), sélectionnez **Machines virtuelles**, ou recherchez et sélectionnez **Machines virtuelles** sur la page **Accueil**.
1. Sélectionnez la machine virtuelle pour laquelle vous souhaitez activer Update Management.
1. Sur la page de la machine virtuelle, sous **OPÉRATIONS**, sélectionnez **Gestion des mises à jour**. Le volet **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle. Cette validation inclut la recherche d’un espace de travail Log Analytics et d’un compte Automation lié, et détermine si la solution Update Management est activée dans l’espace de travail.

Un espace de travail [Log Analytics](../azure-monitor/platform/data-platform-logs.md) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour. L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

Le processus de validation vérifie également si la machine virtuelle est provisionnée avec l’agent Log Analytics et un runbook Worker hybride Automation. Cet agent est utilisé pour communiquer avec Azure Automation et obtenir des informations sur l’état des mises à jour. L’agent nécessite que le port 443 soit ouvert pour communiquer avec le service Azure Automation et télécharger des mises à jour.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../azure-monitor/platform/data-platform-logs.md)
* Un [compte Automation](./automation-offering-get-started.md)
* Un [runbook Worker hybride](./automation-hybrid-runbook-worker.md) (activé sur la machine virtuelle)

Sous **Gestion des mises à jour**, définissez l’emplacement, l’espace de travail Log Analytics et le compte Automation à utiliser. Ensuite, sélectionnez **Activer**. Si ces options ne sont pas disponibles, cela signifie qu’une autre solution Automation est activée pour la machine virtuelle. Dans ce cas, les mêmes espace de travail et compte Automation doivent être utilisés.

![Fenêtre Activer la solution de gestion des mises à jour](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

L’activation de la solution peut prendre quelques minutes. Pendant ce temps, ne fermez pas la fenêtre du navigateur. Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées aux journaux Azure Monitor. Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la gestion des mises à jour activée, le volet **Gestion des mises à jour** s’ouvre. Si des mises à jour sont identifiées comme manquantes, une liste des mises à jour manquantes s’affiche sous l’onglet **Mises à jour manquantes**.

Sous **LIEN D’INFORMATIONS**, sélectionnez le lien de mise à jour pour ouvrir l’article du support de la mise à jour. Vous y trouverez des informations importantes sur la mise à jour.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Cliquez n’importe où sur la mise à jour pour ouvrir le volet **Recherche dans les journaux** pour la mise à jour sélectionnée. La requête pour la recherche dans les journaux est prédéfinie pour cette mise à jour spécifique. Vous pouvez modifier cette requête ou créer votre propre requête afin d’afficher des informations détaillées sur les mises à jour déployées ou manquantes dans votre environnement.

![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurer des alertes

Lors de cette étape, vous allez configurer une alerte afin d’être informé de l’état d’un déploiement de mise à jour.

### <a name="alert-conditions"></a>Conditions d’alerte

Dans votre compte Automation, sous **Supervision** accédez à **Alertes**, puis cliquez sur **+ Nouvelle règle d’alerte**.

Votre compte Automation est déjà sélectionné en tant que ressource. Si vous souhaitez changer, vous pouvez cliquer sur **Sélectionner** puis, dans la page **Sélectionner une ressource**, sélectionner **Comptes Automation** dans la liste déroulante **Filtrer par type de ressource**. Sélectionnez votre compte Automation, puis **Terminé**.

Cliquez sur **Ajouter une condition** pour sélectionner le signal qui convient à votre déploiement de mises à jour. Le tableau suivant présente les détails des deux signaux disponibles pour les déploiements de mises à jour :

|Nom du signal|Dimensions|Description|
|---|---|---|
|**Nombre total d’exécutions de déploiement de mises à jour**|- Nom du déploiement de mises à jour</br>- Statut|Ce signal sert à générer des alertes concernant le statut global d’un déploiement de mises à jour.|
|**Nombre total d’exécutions de déploiement de mises à jour d’ordinateurs**|- Nom du déploiement de mises à jour</br>- Statut</br>- Ordinateur cible</br>- ID d’exécution du déploiement des mises à jour|Ce signal sert à générer des alertes concernant le statut d’un déploiement de mises à jour ciblant des ordinateurs spécifiques|

Pour les valeurs de dimension, sélectionnez une valeur valide dans la liste. Si la valeur que vous recherchez ne figure pas dans la liste, cliquez sur le signe **\+** à côté de la dimension et tapez le nom personnalisé. Vous pouvez ensuite sélectionner la valeur que vous souhaitez rechercher. Si vous souhaitez sélectionner toutes les valeurs d’une dimension, cliquez sur le bouton **Sélectionner \*** . Si vous ne choisissez pas une valeur pour une dimension, celle-ci sera ignorée lors de l’évaluation.

![Configurer la logique du signal](./media/automation-tutorial-update-management/signal-logic.png)

Sous **Logique d’alerte**, pour **Seuil**, entrez **1**. Quand vous avez terminé, cliquez sur **Terminé**.

### <a name="alert-details"></a>Détails de l’alerte

Sous **2. Définissez les détails de l’alerte**, entrez un nom et une description pour l’alerte. Définissez **Gravité** sur **Informations (gravité 2)** pour une exécution réussie, ou sur **Informations (gravité 1)** pour une exécution ayant échoué.

![Configurer la logique du signal](./media/automation-tutorial-update-management/define-alert-details.png)

Sous **Groupes d’action**, sélectionnez **Créer**. Un groupe d’actions est un groupe que vous pouvez utiliser dans plusieurs alertes. Les actions peuvent inclure, sans s’y limiter, les notifications par e-mail, les runbooks, les webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/platform/action-groups.md).

Dans la zone **Nom du groupe d’actions** , entrez un nom pour l’alerte et un nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

Sous **Actions**, entrez un nom pour l’action, tel que **Notifications par e-mail**. Sous **TYPE D’ACTION**, sélectionnez **E-mail/SMS/Push/Voix**. Sous **DÉTAILS**, sélectionnez **Modifier les détails**.

Dans le volet **E-mail/SMS/Push/Voix**, entrez un nom. Sélectionnez la case à cocher **E-mail**, puis entrez une adresse e-mail valide.

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/configure-email-action-group.png)

Dans le volet **E-mail/SMS/Push/Voix**, sélectionnez **OK**. Dans le volet **Ajouter un groupe d’actions**, sélectionnez **OK**.

Pour personnaliser l’objet de l’e-mail d’alerte, sous **Créer une règle**, sous **Personnaliser les actions**, sélectionnez **Objet de l’e-mail**. Lorsque vous avez terminé, cliquez sur **Créer une règle d’alerte**. L’alerte vous avertit quand un déploiement de mises à jour s’est correctement déroulé et précise quelles machines en ont bénéficié.

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Ensuite, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions pour installer les mises à jour. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

>[!NOTE]
>Quand vous planifiez un déploiement de mises à jour, cela entraîne la création d’une ressource de [planification](shared-resources/schedules.md) liée au runbook **Patch-MicrosoftOMSComputers**, qui gère le déploiement des mises à jour sur les machines cibles. Si vous supprimez la ressource de planification du portail Azure, ou si vous utilisez PowerShell après la création du déploiement, le déploiement de mises à jour planifié est interrompu. De plus, une erreur est générée quand vous essayez de le reconfigurer à partir du portail. Vous ne pouvez supprimer la ressource de planification qu’en supprimant la planification de déploiement correspondante.  
>

Pour planifier un nouveau déploiement de mises à jour pour la machine virtuelle, accédez à **Gestion des mises à jour**, puis cliquez sur **Planifier le déploiement de la mise à jour**.

Sous **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : entrez un nom unique pour le déploiement de mises à jour.

* **Système d’exploitation** : sélectionnez le système d’exploitation à cibler pour le déploiement de mises à jour.

* **Groupes à mettre à jour (préversion)**  : Définissez une requête basée sur une combinaison de l’abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour plus d’informations, consultez [Groupes dynamiques](automation-update-management-groups.md)

* **Ordinateurs à mettre à jour** : Sélectionnez une recherche enregistrée, un groupe importé ou choisissez un ordinateur dans la liste déroulante, puis sélectionnez des ordinateurs individuels. Si vous choisissez **Machines**, l’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../azure-monitor/platform/computer-groups.md).

* **Classification des mises à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Pour ce didacticiel, conservez tous les types sélectionnés.

  Les types de classification sont les suivants :

   |OS  |Type  |
   |---------|---------|
   |Windows     | Mises à jour critiques</br>Mises à jour de sécurité</br>Correctifs cumulatifs</br>Packs de fonctionnalités</br>Service Packs</br>Mises à jour de définitions</br>Outils</br>Mises à jour        |
   |Linux     | Mises à jour critiques et de sécurité</br>Autres mises à jour       |

   Pour obtenir la description des types de classification, consultez [Classifications des mises à jour](automation-view-update-assessments.md#update-classifications).

* **Mises à jour à inclure/exclure** : ceci ouvre la page **Inclure/Exclure**. Les mises à jour à inclure ou à exclure sont sous des onglets distincts.

> [!NOTE]
> Il est important de se souvenir que les exclusions sont prioritaires sur les inclusions. Par exemple, si vous définissez une règle d’exclusion de `*`, aucun correctif ou package n’est installé puisque cette règle les exclut tous. Les correctifs exclus sont toujours affichés comme étant manquants sur l’ordinateur. Sur les machines Linux, si un package est inclus, mais qu’il a un package dépendant exclu, le package n’est pas installé.

* **Paramètres de planification** : le volet **Paramètres de planification** s’affiche. L’heure de début par défaut est dans 30 minutes. Vous pouvez définir l’heure de début à tout moment à partir de 10 minutes à l’avenir.

   Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Sous **Récurrence**, sélectionnez **Une fois**. Laissez la valeur par défaut sur 1 jour et sélectionnez **OK**. Cela configure une planification récurrente.

* **Préscripts + postscripts** : sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer les pré-scripts et les post-scripts](pre-post-scripts.md).

* **Fenêtre de maintenance (minutes)**  : Conservez la valeur par défaut. Les fenêtres de maintenance contrôlent la période pendant laquelle les mises à jour doivent être installées. Tenez compte des détails suivants au moment de spécifier une fenêtre de maintenance.

  * Les fenêtres de maintenance contrôlent le nombre de tentatives d’installation des mises à jour.
  * Update Management n’arrête pas l’installation de nouvelles mises à jour si la fin d’une fenêtre de maintenance est proche.
  * Update Management ne met pas fin aux mises à jour en cours si la fenêtre de maintenance est dépassée.
  * Le dépassement de la fenêtre de maintenance sur Windows est souvent le signe que l’installation d’une mise à jour de Service Pack prend beaucoup de temps.

  > [!NOTE]
  > Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations sur la configuration du package, consultez la rubrique [Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Options de redémarrage** : ce paramètre détermine comment les redémarrages doivent être traités. Options disponibles :
  * Redémarrer si nécessaire (par défaut)
  * Toujours redémarrer
  * Ne jamais redémarrer
  * Redémarrer uniquement : les mises à jour ne sont pas installées

> [!NOTE]
> Les clés de Registre répertoriées sous [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent entraîner un événement de redémarrage même si l’option **Contrôle de redémarrage** est définie sur **Ne jamais redémarrer**.

Lorsque vous avez terminé de configurer la planification, sélectionnez **Créer**.

![Volet Paramètres de planification des mises à jour](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

> [!NOTE]
> Update Management prend en charge le déploiement des mises à jour principales et le téléchargement préalable des correctifs. Cela nécessite des modifications sur les systèmes en cours de correction, consultez [first party and pre-download support](automation-configure-windows-update.md) (prise en charge principale et téléchargement préalable) pour apprendre à configurer ces paramètres sur vos systèmes.

Vous pouvez également créer des **déploiements de mises à jour** par programmation. Pour savoir comment créer un **déploiement de mises à jour** avec l’API REST, consultez [Configurations des mises à jour logicielles - Créer](/rest/api/automation/softwareupdateconfigurations/create). Vous pouvez également utiliser un exemple de runbook fourni pour créer un **déploiement de mises à jour** hebdomadaires. Pour en savoir plus sur ce runbook, consultez [Créer un déploiement de mises à jour hebdomadaires pour une ou plusieurs machines virtuelles dans un groupe de ressources](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** sous **Gestion des mises à jour**. Si le déploiement est en cours d’exécution, son état est **En cours**. Une fois le déploiement terminé, s’il a réussi, l’état passe à **Réussi**. En cas d’échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.

Sélectionnez le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Sous **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur la machine virtuelle. Le tableau de droite montre une répartition détaillée de chaque mise à jour et les résultats de l’installation.

La liste suivante présente les valeurs disponibles :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussite** : la mise à jour a réussi.
* **Échec** : la mise à jour a échoué.

Pour afficher toutes les entrées de journal d’activité créées par le déploiement, sélectionnez **Tous les journaux d’activité**.

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

