---
title: Guide pratique pour créer des déploiements de mises à jour pour Azure Automation Update Management
description: Cet article explique comment planifier des déploiements de mises à jour et vérifier leur état.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2a81376b284e0d1df84a69b969335c0e63999a00
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449558"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Guide pratique pour déployer des mises à jour et voir les résultats

Cet article explique comment planifier un déploiement de mises à jour et voir le processus après le déploiement.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

La planification d’un déploiement de mises à jour entraîne la création d’une ressource de [planification](../shared-resources/schedules.md) liée au runbook **Patch-MicrosoftOMSComputers**, qui gère le déploiement des mises à jour sur les machines cibles. Vous devez planifier un déploiement qui suit votre fenêtre de maintenance et de planification de la mise en production pour installer les mises à jour. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

>[!NOTE]
>Si vous supprimez la ressource de planification dans le Portail Azure ou si vous utilisez PowerShell après la création du déploiement, l’opération de suppression arrête le déploiement de mises à jour planifié. De plus, une erreur est générée quand vous essayez de reconfigurer la ressource de planification à partir du Portail. Vous ne pouvez supprimer la ressource de planification qu’en supprimant la planification de déploiement correspondante.  

Pour planifier un nouveau déploiement de mises à jour

1. Dans votre compte Automation, accédez à **Update Management** sous **Gestion des mises à jour**, puis sélectionnez **Planifier le déploiement de la mise à jour**.

2. Sous **Nouveau déploiement de mises à jour**, dans le champ **Nom**, entrez un nom unique pour votre déploiement.

3. Sélectionnez le système d’exploitation à cibler pour le déploiement de mises à jour.

4. Dans la section **Groupes à mettre à jour (préversion)** , définissez une requête qui combine un abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour en savoir plus, consultez [Utiliser des groupes dynamiques avec Update Management](update-mgmt-groups.md).

5. Dans la section **Machines à mettre à jour**, sélectionnez une recherche enregistrée, un groupe importé, ou choisissez **Machines** dans la liste déroulante et sélectionnez des machines spécifiques. Avec cette option, vous pouvez voir la préparation de l’agent Log Analytics pour chaque machine. Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../../azure-monitor/platform/computer-groups.md).

6. Utilisez la section **Classifications des mises à jour** pour spécifier des [classifications des mises à jour](update-mgmt-view-update-assessments.md#work-with-update-classifications) pour les produits. Pour chaque produit, désélectionnez toutes les classifications de mises à jour prises en charge, à l’exception de celles que vous souhaitez inclure dans votre déploiement de mises à jour.

7. Utilisez la section **Inclure/exclure des mises à jour** pour sélectionner des mises à jour spécifiques pour le déploiement. La page Inclure/Exclure affiche les mises à jour par numéro d’ID d’article de la Base de connaissances à inclure ou exclure.
    
   > [!IMPORTANT]
   > N’oubliez pas que les exclusions sont prioritaires par rapport aux inclusions. Par exemple, si vous définissez une règle d’exclusion `*`, Update Management exclut tous les correctifs et packages de l’installation. Les correctifs exclus sont toujours affichés comme étant manquants sur les machines. Sur les machines Linux, si vous incluez un package qui a un package dépendant ayant été exclu, Update Management n’installe pas le package principal.

   > [!NOTE]
   > Vous ne pouvez pas spécifier des mises à jour qui ont été remplacées pour être incluses dans le déploiement des mises à jour.

8. Sélectionnez **Paramètres de planification**. L’heure de début par défaut est dans 30 minutes. Vous pouvez définir l’heure de début à tout moment à partir de 10 minutes à l’avenir.

9. Utilisez le champ **Récurrence** pour spécifier si le déploiement se produit une seule fois ou utilise une planification récurrente, puis sélectionnez **OK**.

10. Dans la section **Préscripts + postscripts (préversion)** , sélectionnez les scripts à exécuter avant et après votre déploiement. Pour plus d’informations, consultez [Gérer des pré-scripts et des post-scripts](update-mgmt-pre-post-scripts.md).
    
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

13. Quand vous avez terminé de configurer la planification du déploiement, sélectionnez **Créer**.

    ![Volet Paramètres de planification des mises à jour](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Vous revenez au tableau de bord d’état. Sélectionnez **Déploiements des mises à jour planifiés** pour afficher la planification de déploiement que vous avez créée.

## <a name="schedule-an-update-deployment-programmatically"></a>Planifier un déploiement de mises à jour par programmation

Pour savoir comment créer un déploiement de mises à jour avec l’API REST, consultez [Configurations des mises à jour logicielles - Créer](/rest/api/automation/softwareupdateconfigurations/create).

Vous pouvez utiliser un exemple de runbook pour créer un déploiement de mises à jour hebdomadaire. Pour en savoir plus sur ce runbook, consultez [Créer un déploiement de mises à jour hebdomadaires pour une ou plusieurs machines virtuelles dans un groupe de ressources](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Vérifier l’état du déploiement

Une fois que le déploiement planifié a démarré, vous pouvez voir son état sous l’onglet **Déploiements des mises à jour** sous **Gestion des mises à jour**. Si le déploiement est en cours d’exécution, son état est **En cours**. Quand le déploiement s’est déroulé correctement, son état passe à **Réussi**. Si une ou plusieurs mises à jour ont échoué pendant le déploiement, l’état passe à **Échec partiel**.

## <a name="view-results-of-a-completed-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour terminé

Quand le déploiement est terminé, vous pouvez le sélectionner pour voir les résultats correspondants.

[ ![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Sous **Résultats des mises à jour**, un récapitulatif indique le nombre total de mises à jour et de résultats du déploiement sur les machines virtuelles cibles. Le tableau de droite montre une répartition détaillée des mises à jour et les résultats de l’installation de chacune d’elles.

Les valeurs disponibles sont :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée car le temps disponible était insuffisant, d’après la durée de la fenêtre de maintenance définie.
* **Non sélectionné** : la mise à jour n’a pas été sélectionnée pour le déploiement.
* **Réussi** : la mise à jour a réussi.
* **Échec** : la mise à jour a échoué.

Pour voir toutes les entrées de journal d’activité créées par le déploiement, sélectionnez **Tous les journaux d’activité**.

Cliquez sur **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur les machines virtuelles cibles.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer des alertes pour vous informer des résultats du déploiement des mises à jour, consultez [Créer des alertes pour Update Management](update-mgmt-configure-alerts.md).