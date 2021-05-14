---
title: Supprimer une DSC et un nœud de State Configuration dans Automation
description: Cet article explique comment supprimer une Desired State Configuration (DSC) affectée et annuler l’inscription d’un nœud géré dans Azure Automation.
titleSuffix: Azure Automation
services: automation
ms.subservice: dsc
ms.date: 04/16/2021
ms.topic: how-to
ms.openlocfilehash: 2db345705933f89a2ce119e65182f88a52f13dc8
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716334"
---
# <a name="how-to-remove-a-configuration-and-node-from-automation-state-configuration"></a>Comment supprimer une configuration et un nœud de State Configuration dans Automation

Cet article explique comment annuler l’inscription d’un nœud géré par State Configuration dans Automation et supprimer en toute sécurité une configuration Desired State Configuration (DSC) PowerShell à partir de nœuds managés. Pour les nœuds Windows et Linux, vous devez [annuler l’inscription du nœud](#unregister-a-node) et [supprimer la configuration](#delete-a-configuration-from-the-azure-portal). Pour les nœuds Linux uniquement, vous pouvez également supprimer les packages DSC des nœuds. Consultez [Supprimer le package DSC d’un nœud Linux](#remove-the-dsc-package-from-a-linux-node).

## <a name="unregister-a-node"></a>Désinscrire un nœud

Si vous ne souhaitez plus qu’un nœud soit géré par State Configuration (DSC), vous pouvez annuler son inscription à partir du portail Azure ou avec Azure PowerShell à l’aide des étapes suivantes.

L’annulation de l’enregistrement d’un nœud à partir du service définit uniquement les paramètres du Gestionnaire de configuration local de sorte que le nœud ne se connecte plus au service. Cela n’affecte pas la configuration qui est actuellement appliquée au nœud et laisse les fichiers associés en place sur le nœud. Vous pouvez éventuellement nettoyer ces fichiers. Consultez [Supprimer une configuration](#delete-a-configuration).

### <a name="unregister-in-the-azure-portal"></a>Se désinscrire dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. À partir de votre compte Automation, sélectionnez **State configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Nœuds**.
1. Sous l’onglet **Nœuds**, sélectionnez le nom du nœud dont vous voulez annuler l’inscription.
1. Sur le volet de ce nœud, cliquez sur **Annuler l'inscription**.

   :::image type="content" source="./media/remove-node-and-configuration-package/unregister-node.png" alt-text="Capture d’écran de la page de détails des nœuds mettant en évidence le bouton Annuler l’inscription." lightbox="./media/remove-node-and-configuration-package/unregister-node.png":::

### <a name="unregister-using-powershell"></a>Annulation d’inscription avec PowerShell

Vous pouvez également annuler l’inscription d’un nœud à l’aide de la cmdlet PowerShell [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

>[!NOTE]
>Si votre organisation utilise toujours les modules AzureRM dépréciés, vous pouvez utiliser [Unregister-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/unregister-azurermautomationdscnode).

## <a name="delete-a-configuration"></a>Supprimer une configuration

Lorsque vous êtes prêt à supprimer un document de configuration DSC importé (qui est un fichier au format MOF ou .mof) qui est affecté à un ou plusieurs nœuds, procédez comme suit.

### <a name="delete-a-configuration-from-the-azure-portal"></a>Supprimer une configuration à partir du portail Azure

Vous pouvez supprimer des configurations pour les nœuds Windows et Linux à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. À partir de votre compte Automation, sélectionnez **State configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **Configuration de l’état (DSC)** , cliquez sur l’onglet **Configurations**, puis sélectionnez le nom de la configuration que vous souhaitez supprimer.

   :::image type="content" source="./media/remove-node-and-configuration-package/configurations-tab.png" alt-text="Capture d’écran de l’onglet Configurations." lightbox="./media/remove-node-and-configuration-package/configurations-tab.png":::

1. Sur la page de détails de la configuration, cliquez sur **Supprimer** pour supprimer la configuration.

   :::image type="content" source="./media/remove-node-and-configuration-package/delete-extension.png" alt-text="Capture d’écran de la suppression d’une extension." lightbox="./media/remove-node-and-configuration-package/delete-extension.png":::

## <a name="manually-delete-a-configuration-file-from-a-node"></a>Supprimer manuellement un fichier de configuration d’un nœud

Si vous ne souhaitez pas utiliser le portail Azure, vous pouvez supprimer manuellement les fichiers de configuration .mof comme suit.

### <a name="delete-a-windows-configuration-using-powershell"></a>Supprimer une configuration Windows à l’aide de PowerShell

Pour supprimer un document de configuration DSC importé (.mof), utilisez la cmdlet [Remove-DscConfigurationDocument](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument).

### <a name="delete-a-linux-configuration"></a>Supprimer une configuration Linux

Les fichiers de configuration sont stockés dans /etc/opt/omi/conf/dsc/configuration/. Supprimez les fichiers .mof dans ce répertoire pour supprimer la configuration du nœud.

## <a name="remove-the-dsc-package-from-a-linux-node"></a>Supprimer le package DSC d’un nœud Linux

Cette étape est facultative. L’annulation d’inscription d’un nœud Linux de State Configuration (DSC) ne supprime pas les packages DSC et OMI de la machine. Utilisez les commandes ci-dessous pour supprimer les packages, ainsi que tous les journaux et leurs données associées.

Pour trouver les noms des packages et d’autres informations pertinentes, consultez le référentiel GitHub [PowerShell Desired State Configuration pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux).

### <a name="rpm-based-systems"></a>Systèmes basés sur RPM

```bash
RPM -e <package name>
``` 

### <a name="dpkg-based-systems"></a>Systèmes basés sur dpkg

```bash
dpkg -P <package name>
```

 ## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez ré-inscrire le nœud ou en inscrire un nouveau, consultez [Inscrire une machine virtuelle devant être gérée par State Configuration](../tutorial-configure-servers-desired-state.md#register-a-vm-to-be-managed-by-state-configuration).

- Si vous souhaitez rajouter la configuration et recompiler, consultez [Compiler des configurations DSC dans Azure Automation State Configuration](../automation-dsc-compile.md).