---
title: Supprimer des machines virtuelles de la fonctionnalité Suivi des modifications et inventaire d’Azure Automation
description: Cet article explique comment supprimer des machines virtuelles de la fonctionnalité Suivi des modifications et inventaire.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84169452"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Supprimer des machines virtuelles de Change Tracking et Inventory

Lorsque vous avez terminé le déploiement des modifications apportées aux machines virtuelles dans votre environnement, vous pouvez supprimer celles-ci de la fonctionnalité [Suivi des modifications et inventaire](change-tracking.md).

## <a name="to-remove-your-vms"></a>Suppression des machines virtuelles

1. À partir de votre compte Automation, sélectionnez **Suivi des modifications** ou **Inventaire** sous **Gestion de la configuration**.

2. Utilisez la commande suivante pour identifier l’UUID d’une machine virtuelle que vous souhaitez supprimer de la gestion.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Dans votre espace de travail Log Analytics, accédez aux recherches enregistrées de la configuration d’étendue `MicrosoftDefaultScopeConfig-ChangeTracking` sous **Général**.

4. Pour la recherche enregistrée `MicrosoftDefaultComputerGroup`, cliquez sur les points de suspension à droite, puis sélectionnez **Modifier**. 

5. Supprimez l’UUID de la machine virtuelle.

6. Répétez les étapes pour toutes les autres machines virtuelles à supprimer.

7. Enregistrez la recherche enregistrée une fois que vous avez fini de la modifier. 

## <a name="next-steps"></a>Étapes suivantes

* Pour continuer à utiliser la fonctionnalité Suivi des modifications et inventaire, consultez [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour résoudre des problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes liés à Suivi des modifications et inventaire](troubleshoot/change-tracking.md).