---
title: Supprimer des machines virtuelles de la fonctionnalité Suivi des modifications et inventaire d’Azure Automation
description: Cet article explique comment supprimer des machines virtuelles de la fonctionnalité Suivi des modifications et inventaire.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 3a39294c2ecfe7b26cb3ef3d65c11cbcd665d220
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654122"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Supprimer des machines virtuelles de Change Tracking et Inventory

Une fois que vous avez terminé le suivi des modifications sur les machines virtuelles de votre environnement, vous pouvez cesser de les gérer avec la fonctionnalité [Suivi des modifications et inventaire](overview.md) . Pour arrêter leur gestion, vous allez modifier la requête de recherche enregistrée `MicrosoftDefaultComputerGroup` dans l’espace de travail Log Analytics qui est lié à votre compte Automation.

## <a name="sign-into-the-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Pour supprimer vos machines virtuelles

1. Dans le portail Azure, lancez **Cloud Shell** dans le volet de navigation en haut du portail. Si vous ne connaissez pas Azure Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../../cloud-shell/overview.md).

2. Utilisez la commande suivante pour identifier l’UUID d’une machine que vous souhaitez supprimer de la gestion.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**. Sélectionnez votre espace de travail dans la liste.

4. Dans votre espace de travail Log Analytics, sélectionnez **Groupes d’ordinateurs** dans le menu de gauche.

5. À partir de **Groupes d’ordinateurs** dans le volet de droite, l’onglet **Groupes enregistrés** s’affiche par défaut.

6. Dans le tableau, cliquez sur l’icône **Exécuter la requête** à droite de l’élément **MicrosoftDefaultComputerGroup** avec la valeur **Catégorie héritée** de **ChangeTracking**.

7. Dans l’éditeur de requêtes, passez en revue la requête et recherchez l’UUID de la machine virtuelle. Supprimez l’UUID de la machine virtuelle et répétez les étapes pour toutes les machines virtuelles que vous souhaitez supprimer.

   > [!NOTE]
   > Pour une protection supplémentaire, avant d’apporter des modifications, veillez à effectuer une copie de la requête. De cette manière, vous pourrez le restaurer si un problème survient.

   Si vous souhaitez commencer avec la requête d’origine et rajouter des machines pour la prise en charge d’une activité de nettoyage ou de maintenance, copiez la requête suivante :

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. Sauvegardez la recherche enregistrée une fois que vous avez fini de la modifier en sélectionnant **Enregistrer > Enregistrer sous** dans la barre supérieure. Lorsque vous y êtes invité, spécifiez les éléments suivants :

    * **Nom** : ChangeTracking__MicrosoftDefaultComputerGroup
    * L’option **Enregistrer en tant que groupe d’ordinateurs** est sélectionnée
    * **Catégorie héritée** : ChangeTracking

>[!NOTE]
>Les machines s’affichent toujours une fois que vous les avez désinscrites, car nous effectuons des rapports pour toutes les machines évaluées au cours des dernières 24 heures. Une fois la machine supprimée, vous devez attendre 24 heures avant qu’elle ne soit plus listée.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver cette fonctionnalité, consultez [Activer Suivi des modifications et inventaire à partir d’un compte Automation](enable-from-automation-account.md), [Activer Suivi des modifications et inventaire à partir du portail Azure](enable-from-portal.md), [Activer Suivi des modifications et inventaire à partir d’un runbook](enable-from-runbook.md) ou [Activer Suivi des modifications et inventaire à partir d’une machine virtuelle Azure](enable-from-vm.md).