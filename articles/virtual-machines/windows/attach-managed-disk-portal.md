---
title: Attacher un disque de données managé à une machine virtuelle Windows - Azure
description: Comment attacher un disque de données managé à une machine virtuelle Windows à l’aide du portail Azure.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b6b29307ce936ee6b95f68d15f383fc9b2f8c40
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692625"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Attacher un disque de données managé à une machine virtuelle Windows à l’aide du portail Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles 


Cet article montre comment attacher un nouveau disque de données managé à une machine virtuelle Windows à l’aide du portail Azure. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher. Pour plus d’informations, consultez [Tailles des machines virtuelles](../sizes.md).


## <a name="add-a-data-disk"></a>Ajouter un disque de données

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez **Machines virtuelles**.
1. Sélectionnez une machine virtuelle dans la liste.
1. Dans le volet **Machine virtuelle**, sélectionnez **Disques**.
1. Dans le volet **Disques**, sélectionnez **Créer et attacher un disque**.
1. Dans les listes déroulantes du nouveau disque, effectuez les sélections de votre choix et nommez le disque.
1. Sélectionnez **Enregistrer** pour créer le disque de données et l’attacher à la machine virtuelle.

## <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

1. Connectez-vous à la machine virtuelle.
1. Sélectionnez le menu **Démarrer** de Windows dans la machine virtuelle en cours d’exécution et entrez **diskmgmt.msc** dans la zone de recherche. La console **Gestion des disques** console s’ouvre.
1. L’outil Gestion des disques détermine que votre nouveau disque n’est pas initialisé et affiche la fenêtre **Initialiser le disque**.
1. Vérifiez que le nouveau disque est sélectionné, puis sélectionnez **OK** pour l’initialiser.
1. Le nouveau disque apparaît comme **non alloué**. Cliquez avec le bouton droit n’importe où sur le disque, puis sélectionnez **Nouveau volume simple**. La fenêtre **Assistant Création d’un volume simple** s’ouvre.
1. Exécutez l’Assistant en conservant tous les paramètres par défaut. Quand vous avez terminé, sélectionnez **Terminer**.
1. Fermez **Gestion des disques**.
1. Une fenêtre contextuelle s’affiche et vous demande de formater le nouveau disque pour que vous puissiez l’utiliser. Sélectionnez **Formater le disque**.
1. Dans la fenêtre **Formater un nouveau disque**, vérifiez les paramètres, puis sélectionnez **Démarrer**.
1. Un avertissement vous informe que le formatage des disques efface toutes les données. Sélectionnez **OK**.
1. Une fois le formatage terminé, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également [attacher un disque de données à l’aide de PowerShell](attach-disk-ps.md).
- Si votre application doit utiliser le lecteur *D:* pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](change-drive-letter.md).