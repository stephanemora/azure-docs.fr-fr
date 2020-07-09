---
title: Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure dans Azure Automation
description: Cet article explique comment utiliser Update Management pour gérer les mises à jour et les correctifs pour vos machines virtuelles Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185787"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure

Cet article explique comment utiliser la fonctionnalité [Update Management](automation-update-management.md) d’Azure Automation pour gérer les mises à jour et les correctifs pour vos machines virtuelles Azure. Pour plus d’informations sur les prix, consultez [Tarification d’Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

> [!NOTE]
> Update Management prend en charge le déploiement des mises à jour tierces et le prétéléchargement des correctifs. Cette prise en charge nécessite la modification de certains paramètres sur les systèmes corrigés. Pour découvrir comment configurer ces paramètres sur vos systèmes, consultez [Configurer les paramètres de Windows Update pour Azure Automation Update Management](automation-configure-windows-update.md).

Avant d’appliquer les procédures décrites dans cet article, vérifiez que vous avez activé Update Management sur vos machines virtuelles à l’aide de l’une des techniques suivantes :

* [Activer Update Management à partir d’un compte Automation](automation-onboard-solutions-from-automation-account.md)
* [Activer Update Management à partir du portail Azure](automation-onboard-solutions-from-browse.md)
* [Activer Update Management à partir d’un runbook](automation-onboard-solutions.md)
* [Activer Update Management à partir d’une machine virtuelle Azure](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limiter l’étendue du déploiement

Update Management utilise une configuration d’étendue au sein de l’espace de travail pour cibler les ordinateurs devant recevoir la mise à jour. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour Update Management](automation-scope-configurations-update-management.md).

## <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Pour afficher une évaluation des mises à jour

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**. 

2. Les mises à jour pour votre environnement sont listées dans la page Gestion des mises à jour. Si des mises à jour sont identifiées comme manquantes, une liste des mises à jour manquantes s’affiche sous l’onglet **Mises à jour manquantes**.

3. Sous **Lien d’information**, sélectionnez le lien d’une mise à jour pour ouvrir l’article de support qui vous donne des informations importantes sur la mise à jour.

    ![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. Cliquez n’importe où ailleurs sur la mise à jour pour ouvrir le volet Recherche dans les journaux. La requête pour la recherche dans les journaux est prédéfinie pour cette mise à jour spécifique. Vous pouvez modifier cette requête ou créer votre propre requête pour afficher des informations détaillées.

    ![Afficher l’état des mises à jour](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Configurer des alertes

Effectuez les étapes ci-dessous pour configurer des alertes afin de vous informer de l’état d’un déploiement de mises à jour :

1. Dans votre compte Automation, accédez à **Alertes** sous **Supervision**, puis cliquez sur **Nouvelle règle d’alerte**.

2. Dans la page Créer une règle d’alerte, votre compte Automation est déjà sélectionné en tant que ressource. Si vous souhaitez le changer, cliquez sur **Modifier la ressource**. 

3. Dans la page Sélectionner une ressource, choisissez **Comptes Automation** dans le menu déroulant **Filtrer par type de ressource**. 

4. Sélectionnez le compte Automation que vous souhaitez utiliser, puis cliquez sur **Terminé**.

5. Cliquez sur **Ajouter une condition** pour sélectionner le signal qui convient à votre déploiement de mises à jour. Le tableau suivant présente les détails des deux signaux disponibles.

    |Nom du signal|Dimensions|Description
    |---|---|---|
    |`Total Update Deployment Runs`|- Nom du déploiement de mises à jour<br>- Statut    |Génère des alertes concernant le statut global d’un déploiement de mises à jour.|
    |`Total Update Deployment Machine Runs`|- Nom du déploiement de mises à jour</br>- Statut</br>- Ordinateur cible</br>- ID d’exécution du déploiement des mises à jour    |Génère des alertes concernant le statut d’un déploiement de mises à jour ciblant des machines spécifiques.|

6. Pour une dimension, sélectionnez une valeur valide dans la liste. Si la valeur souhaitée ne figure pas dans la liste, cliquez sur **\+** à côté de la dimension et tapez le nom personnalisé. Sélectionnez ensuite la valeur à rechercher. Si vous voulez sélectionner toutes les valeurs d’une dimension, cliquez sur le bouton **Sélectionner \*** . Si vous ne choisissez aucune valeur de dimension, Update Management ignore cette dimension.

    ![Configurer la logique du signal](./media/automation-tutorial-update-management/signal-logic.png)

7. Sous **Logique d’alerte**, entrez les valeurs dans les champs **Agrégation du temps** et **Seuil**, puis cliquez sur **Terminé**.

8. Dans le volet suivant, entrez un nom et une description pour l’alerte.

9. Définissez le champ **Gravité** sur **Information (gravité 2)** pour une exécution réussie, ou sur **Information (gravité 1)** pour une exécution ayant échoué.

    ![Configurer la logique du signal](./media/automation-tutorial-update-management/define-alert-details.png)

10. Cliquez sur **Oui** ou **Non**, en fonction de la façon dont vous souhaitez activer la règle d’alerte.

11. Si vous ne souhaitez pas recevoir d’alertes pour cette règle, sélectionnez **Supprimer les alertes**.

## <a name="configure-action-groups-for-your-alerts"></a>Configurer des groupes d’actions pour vos alertes

Une fois vos alertes configurées, vous pouvez configurer un groupe d’actions à exécuter quand plusieurs alertes sont déclenchées. Les actions peuvent inclure les notifications par e-mail, les runbooks, les webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/platform/action-groups.md).

1. Sélectionnez une alerte, puis sélectionnez **Créer** sous **Groupes d’actions**. 

2. Entrez un nom complet et un nom abrégé pour le groupe d’actions. Update Management utilise le nom abrégé lors de l’envoi de notifications pour le groupe spécifié.

3. Sous **Actions**, entrez un nom qui spécifie l’action, par exemple **Notification par e-mail**. 

4. Pour **Type d’action**, sélectionnez le type approprié, par exemple **E-mail/SMS/Push/Voix**. 

5. Cliquez sur **Modifier les détails**.

6. Renseignez le volet correspondant à votre type d’action. Par exemple, si vous utilisez **E-mail/SMS/Push/Voix**, entrez un nom d’action, cochez la case **E-mail**, entrez une adresse e-mail valide, puis cliquez sur **OK**.

    ![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. Dans le volet Ajouter un groupe d’actions, cliquez sur **OK**.

8. Pour un e-mail d’alerte, vous pouvez personnaliser l’objet de l’e-mail. Sélectionnez **Personnaliser les actions** sous **Créer une règle**, puis sélectionnez **Objet de l’e-mail**. 

9. Quand vous avez terminé, cliquez sur **Créer une règle d’alerte**. 

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

La planification d’un déploiement de mises à jour entraîne la création d’une ressource de [planification](shared-resources/schedules.md) liée au runbook **Patch-MicrosoftOMSComputers**, qui gère le déploiement des mises à jour sur les machines cibles. Vous devez planifier un déploiement qui suit votre fenêtre de maintenance et de planification de la mise en production pour installer les mises à jour. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

>[!NOTE]
>Si vous supprimez la ressource de planification dans le Portail Azure ou si vous utilisez PowerShell après la création du déploiement, l’opération de suppression arrête le déploiement de mises à jour planifié. De plus, une erreur est générée quand vous essayez de reconfigurer la ressource de planification à partir du Portail. Vous ne pouvez supprimer la ressource de planification qu’en supprimant la planification de déploiement correspondante.  

Pour planifier un nouveau déploiement de mises à jour

1. Dans votre compte Automation, accédez à **Update Management** sous **Gestion des mises à jour**, puis sélectionnez **Planifier le déploiement de la mise à jour**.

2. Sous **Nouveau déploiement de mises à jour**, utilisez le champ **Nom** pour entrer un nom unique pour votre déploiement.

3. Sélectionnez le système d’exploitation à cibler pour le déploiement de mises à jour.

4. Dans la section **Groupes à mettre à jour (préversion)** , définissez une requête qui combine un abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour en savoir plus, consultez [Utiliser des groupes dynamiques avec Update Management](automation-update-management-groups.md).

5. Dans la section **Machines à mettre à jour**, sélectionnez une recherche enregistrée, un groupe importé, ou choisissez **Machines** dans la liste déroulante et sélectionnez des machines spécifiques. Avec cette option, vous pouvez voir la préparation de l’agent Log Analytics pour chaque machine. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../azure-monitor/platform/computer-groups.md).

6. Utilisez la section **Classifications des mises à jour** pour spécifier des [classifications des mises à jour](automation-view-update-assessments.md#work-with-update-classifications) pour les produits. Pour chaque produit, désélectionnez toutes les classifications de mises à jour prises en charge, à l’exception de celles que vous souhaitez inclure dans votre déploiement de mises à jour.

7. Utilisez la section **Inclure/exclure des mises à jour** pour sélectionner des mises à jour spécifiques pour le déploiement. La page Inclure/Exclure affiche les mises à jour par numéro d’ID d’article de la Base de connaissances à inclure ou exclure. 
    
   > [!IMPORTANT]
   > N’oubliez pas que les exclusions sont prioritaires par rapport aux inclusions. Par exemple, si vous définissez une règle d’exclusion `*`, Update Management exclut tous les correctifs et packages de l’installation. Les correctifs exclus sont toujours affichés comme étant manquants sur les machines. Sur les machines Linux, si vous incluez un package qui a un package dépendant ayant été exclu, Update Management n’installe pas le package principal.

   > [!NOTE]
   > Vous ne pouvez pas spécifier des mises à jour qui ont été remplacées pour être incluses dans le déploiement des mises à jour.

8. Sélectionnez **Paramètres de planification**. L’heure de début par défaut est dans 30 minutes. Vous pouvez définir l’heure de début à tout moment à partir de 10 minutes à l’avenir.

9. Utilisez le champ **Répétition** pour spécifier si le déploiement se produit une seule fois ou utilise une planification récurrente, puis cliquez sur **OK**.

10. Dans la section **Préscripts + postscripts (préversion)** , sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer des pré-scripts et des post-scripts](pre-post-scripts.md).
    
11. Utilisez le champ **Fenêtre de maintenance (minutes)** afin de spécifier la durée autorisée pour l’installation des mises à jour. Tenez compte des détails suivants au moment de spécifier une fenêtre de maintenance :

    * Les fenêtres de maintenance contrôlent le nombre de mises à jour installées.
    * Update Management n’arrête pas l’installation de nouvelles mises à jour si la fin d’une fenêtre de maintenance est proche.
    * Update Management ne met pas fin aux mises à jour en cours si la fenêtre de maintenance est dépassée.
    * Le dépassement de la fenêtre de maintenance sur Windows est souvent le signe que l’installation d’une mise à jour de Service Pack prend beaucoup de temps.

    > [!NOTE]
    > Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package `Unattended-Upgrade` pour désactiver les mises à jour automatiques. Pour plus d’informations sur la façon de configurer le package, consultez la [rubrique sur les mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Utilisez le champ **Options de redémarrage** pour spécifier la manière de gérer les redémarrages au cours du déploiement. Les options suivantes sont disponibles : 
    * Redémarrer si nécessaire (option par défaut)
    * Toujours redémarrer
    * Ne jamais redémarrer
    * Redémarrer uniquement ; cette option n’installe pas les mises à jour

    > [!NOTE]
    > Les clés de Registre listées sous [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent provoquer un événement de redémarrage même si **Options de redémarrage** est défini sur **Ne jamais redémarrer**.

13. Quand vous avez terminé de configurer la planification du déploiement, cliquez sur **Créer**.

    ![Volet Paramètres de planification des mises à jour](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

## <a name="schedule-an-update-deployment-programmatically"></a>Planifier un déploiement de mises à jour par programmation

Pour savoir comment créer un déploiement de mises à jour avec l’API REST, consultez [Configurations des mises à jour logicielles - Créer](/rest/api/automation/softwareupdateconfigurations/create). 

Vous pouvez utiliser un exemple de runbook pour créer un déploiement de mises à jour hebdomadaire. Pour en savoir plus sur ce runbook, consultez [Créer un déploiement de mises à jour hebdomadaires pour une ou plusieurs machines virtuelles dans un groupe de ressources](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Vérifier l’état du déploiement

Une fois que le déploiement planifié a démarré, vous pouvez voir son état sous l’onglet **Déploiements des mises à jour** sous **Gestion des mises à jour**. Si le déploiement est en cours d’exécution, son état est **En cours**. Quand le déploiement s’est déroulé correctement, son état passe à **Réussi**. Si une ou plusieurs mises à jour ont échoué pendant le déploiement, l’état passe à **Échec partiel**.

## <a name="view-results-of-a-completed-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour terminé

Une fois le déploiement terminé, vous pouvez le sélectionner pour afficher son tableau de bord.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Sous **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur les machines virtuelles cibles. Le tableau de droite montre une répartition détaillée des mises à jour et les résultats de l’installation de chacune d’elles.

Les valeurs disponibles sont :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée car le temps disponible était insuffisant, d’après la durée de la fenêtre de maintenance définie.
* **Non sélectionné** : la mise à jour n’a pas été sélectionnée pour le déploiement.
* **Réussi** : la mise à jour a réussi.
* **Échec** : la mise à jour a échoué.

Pour voir toutes les entrées de journal d’activité créées par le déploiement, sélectionnez **Tous les journaux d’activité**.

Cliquez sur **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur les machines virtuelles cibles.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="view-the-deployment-alert"></a>Afficher l’alerte de déploiement

Une fois le déploiement de mises à jour terminé, vous recevez l’alerte que vous avez spécifiée lors de la configuration du déploiement. Par exemple, voici un e-mail confirmant un correctif.

![Configurer l’e-mail du groupe d’actions](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les configurations d’étendue, consultez [Utiliser des configurations d’étendue pour Update Management](automation-scope-configurations-update-management.md).
* Si vous avez besoin de rechercher dans les journaux d’activité stockés dans votre espace de travail Log Analytics, consultez [Recherches dans les journaux d’Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Si vous en avez terminé avec les déploiements, consultez [Dissocier un espace de travail d’un compte Automation pour Update Management](automation-unlink-workspace-update-management.md).
* Pour supprimer vos machines virtuelles d’Update Management, consultez [Supprimer des machines virtuelles d’Update Management](automation-remove-vms-from-update-management.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).
