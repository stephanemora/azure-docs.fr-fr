---
title: Activer la solution Start/stop VMs during off-hours dans Azure Automation
description: Cet article explique comment activer la solution Azure Automation de démarrage/arrêt de vos machines virtuelles Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096960"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Activer la solution Start/stop VMs dans Azure Automation

Procédez comme suit pour ajouter la solution **Start/Stop VMs during off-hours** à un compte Automation nouveau ou existant et à l’espace de travail Log Analytics lié. Au terme du processus d'intégration, configurez les variables pour personnaliser la solution.

>[!NOTE]
>Pour utiliser cette solution avec des machines virtuelles Classic, vous avez besoin d’un compte d’identification Classic, lequel n’est pas créé par défaut. Pour savoir comment créer un compte d’identification Classic, consultez [Créer un compte d’identification Classic](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Activer la solution

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez et sélectionnez **Comptes Automation**.

3. Dans la page Comptes Automation, sélectionnez votre compte Automation dans la liste.

4. À partir du compte Automation, sélectionnez **Start/Stop VM** sous **Ressources associées**. Vous pouvez alors cliquer sur **En savoir plus sur la solution et l’activer**. Si vous avez déjà une solution Start/Stop VM déployée, sélectionnez-la en cliquant sur **Gérer la solution** et en la recherchant dans la liste.

   ![Activer à partir d’un compte Automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Vous pouvez également la créer n’importe où sur le Portail Azure en cliquant sur **Créer une ressource**. Dans la page Place de marché, saisissez un mot clé, tel que **Démarrer** ou **Arrêter/Démarrer**. Au fur et à mesure de la saisie, la liste est filtrée. Vous pouvez également saisir un ou plusieurs des mots clés à partir du nom complet de la solution, puis appuyer sur la touche Entrée. Sélectionnez **Start/Stop VMs during off-hours** dans les résultats de la recherche.

5. Sur la page **Start/Stop VMs during off-hours** de la solution sélectionnée, vérifiez les informations résumées, puis cliquez sur **Créer**.

   ![Portail Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. La page Ajouter une solution s’affiche. Vous êtes invité à configurer la solution pour pouvoir l’importer dans votre abonnement Automation.

   ![Page Ajouter une solution de VM Management (Gestion de machines virtuelles)](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Sur la page Ajouter une solution, sélectionnez **Espace de travail**. Sélectionnez un espace de travail Log Analytics lié au même abonnement Azure que celui dans lequel le compte Automation se trouve. Si vous ne disposez pas d’espace de travail, sélectionnez **Créer un espace de travail**. Sur la page Espace de travail Log Analytics, suivez les étapes suivantes :

   - Spécifiez un nom pour le nouvel espace de travail Log Analytics, comme**ContosoLAWorkspace**.
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Sous **Groupe de ressources**, vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà.
   - Sélectionnez un **emplacement**.
   - Sélectionner un **niveau de tarification**. Choisissez l’option **Par Go (autonome)** . Les journaux Azure Monitor ont mis à jour les [tarifs](https://azure.microsoft.com/pricing/details/log-analytics/), et le niveau Par Go est la seule option disponible.

   > [!NOTE]
   > Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
   >
   > Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

8. Après avoir entré les informations requises sur la page Espace de travail Log Analytics, cliquez sur **Créer**. Vous pouvez suivre sa progression sous **Notifications** dans le menu, qui vous renvoie à la page Ajouter une solution une fois terminé.

9. Sur la page Ajouter une solution, sélectionnez **Compte Automation**. Si vous créez un espace de travail Log Analytics, vous pouvez créer un compte Automation associé ou en sélectionner un qui ne soit pas déjà lié à un espace de travail Log Analytics. Sélectionnez un compte Automation existant ou cliquez sur **Créer un compte Automation**, puis, sur la page Ajouter un compte Automation, indiquez les informations suivantes :
 
   - Dans le champ **Nom**, saisissez le nom du compte Automation.

     Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail Log Analytics sélectionné. et ne peuvent pas être modifiées. Un compte d’identification Azure est la méthode d’authentification par défaut pour les runbooks inclus dans cette solution. Après avoir cliqué sur **OK**, les options de configuration sont validées et le compte Automation est créé. Vous pouvez suivre la progression sous **Notifications** dans le menu.

10. Enfin, sur la page Ajouter une solution, sélectionnez **Configuration**. La page Paramètres s’affiche.

    ![Page Paramètres pour la solution](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Ce volet vous permet de :
  
   - Spécifier les **noms des groupes de ressources cibles**. Ces valeurs sont les noms des groupes de ressources qui contiennent les machines virtuelles devant être gérées par cette solution. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Si vous souhaitez cibler les machines virtuelles de tous les groupes de ressources de l’abonnement, l’utilisation d’un caractère générique est prise en charge. Cette valeur est stockée dans les variables **External_Start_ResourceGroupNames** et **External_Stop_ResourceGroupnames**.
  
   - Spécifier la **liste d’exclusion de machines virtuelles (chaîne)** . Cette valeur est le nom d’une ou de plusieurs machines virtuelles appartenant au groupe de ressources cible. Vous pouvez entrer plusieurs noms en les séparant par des virgules (les valeurs ne respectent pas la casse). Les caractères génériques sont pris en charge. Cette valeur est stockée dans la variable **External_ExcludeVMNames**.
  
   - Sélectionner une **planification**. Sélectionnez une date et une heure pour votre planification. Un calendrier quotidien récurrent sera créé, commençant à l’heure que vous avez sélectionnée. La sélection d’une autre région n’est pas possible. Pour configurer la planification sur votre propre fuseau horaire après la configuration de la solution, consultez [Modification de la planification de démarrage et d’arrêt](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Pour recevoir des **notifications par e-mail** de la part d’un groupe d’actions, acceptez la valeur par défaut **Oui**, puis fournissez une adresse e-mail valide. Si vous sélectionnez **Non**, mais décidez ultérieurement de recevoir les notifications par e-mail, vous pouvez mettre à jour le [groupe d’actions](../azure-monitor/platform/action-groups.md) qui est créé en y ajoutant des adresses e-mail valides, séparées par une virgule. Activez également les règles d’alerte suivantes :

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > La valeur par défaut pour les **noms des groupes de ressources cibles** est un **&ast;** . Elle cible toutes les machines virtuelles dans un abonnement. Si vous ne souhaitez pas que la solution cible toutes les machines virtuelles dans votre abonnement, vous devez définir cette valeur sur une liste de noms de groupes de ressources avant d’activer les planifications.

11. Après avoir configuré les paramètres initiaux requis pour la solution, cliquez sur **OK** pour fermer la page Paramètres et sélectionnez **Créer**. 

Quand tous les paramètres sont validés, la solution est déployée dans votre abonnement. Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

> [!NOTE]
> Si vous avez un abonnement Azure Cloud Solution Provider (Azure CSP), une fois le déploiement terminé, dans votre compte Automation, accédez à **Variables** sous **Ressources partagées** et définissez la variable [External_EnableClassicVMs](automation-solution-vm-management.md#variables) sur **False**. La solution arrête de rechercher des ressources de machine virtuelle classiques.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que la solution est activée, vous pouvez la [configurer](automation-solution-vm-management-config.md) pour qu'elle prenne en charge vos exigences de gestion des machines virtuelles.