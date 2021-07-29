---
title: Supprimer des machines virtuelles d’Azure Automation Update Management
description: Cet article explique comment supprimer des machines gérées avec Update Management.
services: automation
ms.topic: conceptual
ms.date: 06/03/2021
ms.custom: mvc
ms.openlocfilehash: 621367ba04893e7a8030b4a284125a6247c5d091
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854969"
---
# <a name="remove-vms-from-update-management"></a>Supprimer des machines virtuelles d’Update Management

Lorsque vous avez terminé de gérer les mises à jour sur vos machines virtuelles dans votre environnement, vous pouvez arrêter de gérer les machines virtuelles grâce à la fonctionnalité [Update Management](overview.md). Pour arrêter leur gestion, vous allez modifier la requête de recherche enregistrée `MicrosoftDefaultComputerGroup` dans l’espace de travail Log Analytics qui est lié à votre compte Automation.

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

6. Dans le tableau, cliquez sur l’icône **Exécuter la requête** à droite de l’élément **MicrosoftDefaultComputerGroup** avec la valeur **Catégorie héritée** de **Mises à jour**.

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

    * **Name** : Updates__MicrosoftDefaultComputerGroup
    * L’option **Enregistrer en tant que groupe d’ordinateurs** est sélectionnée
    * **Catégorie héritée** : Mises à jour

>[!NOTE]
>Les machines s’affichent toujours une fois que vous les avez désinscrites, car nous effectuons des rapports pour toutes les machines évaluées au cours des dernières 24 heures. Une fois la machine supprimée, vous devez attendre 24 heures avant qu’elle ne soit plus listée.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver la gestion de votre machine virtuelle, consultez [Activer Update Management à partir du portail Azure](enable-from-portal.md) ou [Activer Update Management à partir d’une machine virtuelle Azure](enable-from-vm.md).