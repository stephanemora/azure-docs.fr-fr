---
title: Supprimer des machines virtuelles d’Azure Automation Update Management
description: Cet article explique comment supprimer des machines gérées avec Update Management.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913190"
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

4. Dans votre espace de travail Log Analytics, sélectionnez **Paramètres avancés** puis sélectionnez **Groupes d’ordinateurs** dans le menu de gauche.

5. À partir de **Groupes d’ordinateurs** dans le volet de droite, sélectionnez **Groupes sauvegardés**.

6. Dans le tableau, pour la requête de recherche enregistrée **Updates:MicrosoftDefaultComputerGroup**, cliquez sur l’icône **Afficher les membres** pour lancer l’exécution et voir ses membres.

7. Dans l’éditeur de requêtes, passez en revue la requête et recherchez l’UUID de la machine virtuelle. Supprimez l’UUID de la machine virtuelle et répétez les étapes pour toutes les machines virtuelles que vous souhaitez supprimer.

8. Sauvegardez la recherche enregistrée une fois que vous avez fini de la modifier en sélectionnant **Enregistrer** dans la barre supérieure. Lorsque vous y êtes invité, spécifiez les éléments suivants :

    * **Nom** : MicrosoftDefaultComputerGroup
    * **Enregistrer sous** : Fonction
    * **Alias** : Updates__MicrosoftDefaultComputerGroup
    * **Catégorie** : Mises à jour

>[!NOTE]
>Les machines s’affichent toujours une fois que vous les avez désinscrites, car nous effectuons des rapports pour toutes les machines évaluées au cours des dernières 24 heures. Une fois la machine supprimée, vous devez attendre 24 heures avant qu’elle ne soit plus listée.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver la gestion de votre machine virtuelle, consultez [Activer Update Management à partir du portail Azure](enable-from-portal.md) ou [Activer Update Management à partir d’une machine virtuelle Azure](enable-from-vm.md).