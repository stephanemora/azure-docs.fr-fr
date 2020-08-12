---
title: Supprimer des machines virtuelles d’Azure Automation Update Management
description: Cet article explique comment supprimer des machines gérées avec Update Management.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449519"
---
# <a name="remove-vms-from-update-management"></a>Supprimer des machines virtuelles d’Update Management

Lorsque vous avez terminé de gérer les mises à jour sur vos machines virtuelles dans votre environnement, vous pouvez arrêter de gérer les machines virtuelles grâce à la fonctionnalité [Update Management](update-mgmt-overview.md).

## <a name="sign-into-the-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Pour supprimer vos machines virtuelles

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Utilisez la commande suivante pour identifier l’UUID d’une machine que vous souhaitez supprimer de la gestion.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Dans votre espace de travail Log Analytics, accédez aux recherches enregistrées de la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates` sous **Général**.

4. Pour la recherche enregistrée `MicrosoftDefaultComputerGroup`, cliquez sur les points de suspension à droite, puis sélectionnez **Modifier**.

5. Supprimez l’UUID de la machine virtuelle.

6. Répétez les étapes pour toutes les autres machines virtuelles à supprimer.

7. Enregistrez la recherche enregistrée une fois que vous avez fini de la modifier.

>[!NOTE]
>Les machines s’affichent toujours une fois que vous les avez désinscrites, car nous effectuons des rapports pour toutes les machines évaluées au cours des dernières 24 heures. Une fois la machine déconnectée, vous devez attendre 24 heures avant qu’elle ne soit plus répertoriée.

## <a name="next-steps"></a>Étapes suivantes

Pour réactiver la gestion de votre machine virtuelle, consultez [Activer Update Management à partir du portail Azure](update-mgmt-enable-portal.md) ou [Activer Update Management à partir d’une machine virtuelle Azure](update-mgmt-enable-vm.md).