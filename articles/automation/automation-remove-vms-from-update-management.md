---
title: Supprimer des machines virtuelles d’Azure Automation Update Management
description: Cet article explique comment supprimer des machines virtuelles d’Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836649"
---
# <a name="remove-vms-from-update-management"></a>Supprimer des machines virtuelles d’Update Management

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

Lorsque vous avez terminé le déploiement des mises à jour sur les machines virtuelles de votre environnement, vous pouvez les supprimer à l’aide de la fonctionnalité [Update Management](automation-update-management.md).

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Utilisez la commande suivante pour identifier l’UUID d’une machine virtuelle que vous souhaitez supprimer de la gestion.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Dans votre espace de travail Log Analytics sous **Général**, accédez aux recherches enregistrées.

4. Pour la recherche enregistrée `MicrosoftDefaultComputerGroup`, cliquez sur les points de suspension à droite, puis sélectionnez **Modifier**. 

5. Supprimez l’UUID de la machine virtuelle.

6. Répétez les étapes pour toutes les autres machines virtuelles à supprimer.

7. Enregistrez la recherche enregistrée une fois que vous avez fini de la modifier. 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation d’Update Management, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour résoudre des problèmes d’ordre général concernant la fonctionnalité, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).