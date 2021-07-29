---
title: Activer Start/Stop VMs during off-hours d’Azure Automation
description: Cet article explique comment activer la fonctionnalité Start/Stop VMs during off-hours pour vos machines virtuelles Azure.
services: automation
ms.subservice: process-automation
ms.date: 05/18/2021
ms.topic: conceptual
ms.openlocfilehash: 0db68a72b3b44f9febb7cdef546545b5b549ddae
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069379"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Activer Start/Stop VMs during off-hours

Effectuez les étapes de cette rubrique dans l’ordre pour activer la fonctionnalité Start/Stop VMs during off-hours pour les machines virtuelles à l’aide d’un compte Automation nouveau ou existant et d’un espace de travail Log Analytics lié. Au terme du processus de configuration, configurez les variables pour personnaliser la fonctionnalité.

>[!NOTE]
>Pour utiliser cette fonctionnalité avec des machines virtuelles classiques, vous avez besoin d’un compte d’identification Classic, lequel n’est pas créé par défaut. Voir [Créer un compte d’identification Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-and-configure"></a>Activer et configurer 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez et sélectionnez **Comptes Automation**.
3. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
4. À partir du compte Automation, sélectionnez **Start/Stop VM** sous **Ressources associées**. Vous pouvez alors cliquer sur **En savoir plus sur la solution et l’activer**. Si vous avez déjà déployé la fonctionnalité, vous pouvez cliquer sur **Gérer la solution** et la trouver dans la liste.

   ![Activer à partir d’un compte Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Vous pouvez également créer la ressource n’importe où sur le portail Azure en cliquant sur **Créer une ressource**. Dans la page Place de marché, saisissez un mot clé, tel que **Démarrer** ou **Arrêter/Démarrer**. Au fur et à mesure de la saisie, la liste est filtrée. Vous pouvez également saisir un ou plusieurs mots clés du nom complet de la fonctionnalité, puis appuyer sur la touche **Entrée**. Sélectionnez **Start/Stop VMs during off-hours** dans les résultats de la recherche.

5. Sur la page Start/Stop VMs during off-hours du déploiement sélectionné, vérifiez les informations résumées, puis cliquez sur **Créer**.

   ![Portail Azure](media/automation-solution-vm-management/azure-portal-01.png)

   Une fois la ressource créée, la page Ajouter une solution s’affiche. Vous êtes invité à configurer la fonctionnalité pour pouvoir l’importer dans votre compte Automation.

   ![Page Ajouter une solution de Gestion de machines virtuelles](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

6. Sur la page **Ajouter une solution**, sélectionnez **Espace de travail**. Sélectionnez un espace de travail Log Analytics dans la liste. S’il n’existe pas de compte Automation dans la même région prise en charge que l’espace de travail, vous pouvez créer un nouveau compte Automation à l’étape suivante. 

   > [!NOTE]
   > Lors de l’activation de fonctionnalités, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

7. Sur la **page Ajouter une solution**, si un compte Automation n’est pas disponible dans la région prise en charge en tant qu’espace de travail, sélectionnez **Compte Automation**. Vous pouvez créer un compte Automation à associer en sélectionnant **Créer un compte Automation**. Ensuite, sur la page **Ajouter un compte Automation**, indiquez le nom du compte Automation dans le champ **Nom**.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail Log Analytics sélectionné. Vous ne pouvez pas modifier ces options. Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus avec la fonctionnalité. 
    
    Après avoir cliqué sur **OK**, les options de configuration sont validées et le compte Automation est créé. Vous pouvez suivre la progression sous **Notifications** dans le menu.

8. Sur la page Ajouter une solution, sélectionnez **Configurer les paramètres**. La page **Paramètres** s’affiche.

    ![Page Paramètres pour la solution](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

9. Spécifiez une valeur pour le champ **Noms des groupes de ressources cibles**. Le champ définit les noms de groupes qui contiennent des machines virtuelles que la fonctionnalité doit gérer. Vous pouvez entrer plusieurs noms et les séparer par des virgules (les valeurs ne respectent pas la casse). Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge. Les valeurs sont stockées dans les variables `External_Start_ResourceGroupNames` et `External_Stop_ResourceGroupNames`.

    > [!IMPORTANT]
    > La valeur par défaut pour les **noms des groupes de ressources cibles** est un **&ast;** . Ce paramètre cible toutes les machines virtuelles d’un abonnement. Si vous ne souhaitez pas que la fonctionnalité cible toutes les machines virtuelles de votre abonnement, vous devez fournir une liste de noms de groupes de ressources avant de sélectionner une planification.
  
10. Spécifiez une valeur pour le champ **Liste d’exclusion de machines virtuelles (chaîne)** . Cette valeur est le nom d’une ou de plusieurs machines virtuelles appartenant au groupe de ressources cible. Vous pouvez entrer plusieurs noms et les séparer par des virgules (les valeurs ne respectent pas la casse). Les caractères génériques sont pris en charge. La valeur est stockée dans la variable `External_ExcludeVMNames`.
  
11. Utilisez le champ **Planifier** pour sélectionner une planification de la gestion des machines virtuelles par la fonctionnalité. Sélectionnez une date et une heure de début pour votre planification, afin de créer une planification quotidienne récurrente qui commence à l’heure choisie. La sélection d’une autre région n’est pas possible. Pour configurer la planification sur votre propre fuseau horaire après la configuration de la fonctionnalité, consultez [Modifier les planifications de démarrage et d’arrêt](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

12. Pour recevoir des notifications par e-mail de la part d’un [groupe d’actions](../azure-monitor/alerts/action-groups.md), acceptez la valeur par défaut **Oui** dans le champ **Notifications par e-mail**, puis fournissez une adresse e-mail valide. Si vous sélectionnez **Non**, mais décidez ultérieurement de recevoir les notifications par e-mail, vous pouvez mettre à jour le groupe d’actions créé en y ajoutant des adresses e-mail valides, séparées par des virgules. Les règles d’alerte suivantes sont créées dans l’abonnement :

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

13. Après avoir configuré les paramètres initiaux requis pour la fonctionnalité, cliquez sur **OK** pour fermer la page **Paramètres**.

14. Cliquez sur **Créer**. Quand tous les paramètres sont validés, la fonctionnalité est déployée dans votre abonnement. Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

    > [!NOTE]
    > Si vous avez un abonnement Azure Cloud Solution Provider (Azure CSP), une fois le déploiement terminé, dans votre compte Automation, accédez à **Variables** sous **Ressources partagées** et définissez la variable [External_EnableClassicVMs](automation-solution-vm-management.md#variables) sur **False**. La solution arrête de rechercher des ressources de machine virtuelle classiques.

## <a name="create-alerts"></a>Créez des alertes

Start/Stop VMs during off-hours n’inclut aucun ensemble prédéfini d’alertes de travail Automation. Passez en revue [Transférer les données de travail dans Journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md#azure-monitor-log-records) pour en savoir plus sur les données de journal transférées à partir du compte Automation en rapport avec les résultats de la tâche de runbook et sur la création d’alertes pour les travaux ayant échoué pour prendre en charge vos procédures et processus DevOps ou opérationnels.

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer la fonctionnalité, consultez [Configurer Start/Stop VMs during off-hours](automation-solution-vm-management-config.md).
* Pour résoudre les erreurs liées à la fonctionnalité, consultez [Résoudre les problèmes liés à Start/Stop VMs during off-hours](troubleshoot/start-stop-vm.md).
