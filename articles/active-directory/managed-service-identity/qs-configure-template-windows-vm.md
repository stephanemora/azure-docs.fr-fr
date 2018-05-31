---
title: Configurer MSI sur une machine virtuelle Azure à l’aide d’un modèle
description: Instructions détaillées sur la configuration de MSI (Managed Service Identity) sur une machine virtuelle Azure, à l’aide d’un modèle Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 8c955e6ad9d47c6963a1c136600761fddee03835
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930280"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurer une identité MSI de machine virtuelle à l’aide d’un modèle

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment effectuer les opérations Managed Service Identity suivantes sur une machine virtuelle Azure, à l’aide du modèle de déploiement Azure Resource Manager :

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Comme pour le portail Azure et le script, les modèles [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. L’activation d’une identité attribuée au système ou à l’utilisateur à une machine virtuelle nouvelle ou existante s’effectuent de la même manière. En outre, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements.

## <a name="system-assigned-identity"></a>identité attribuée au système

Dans cette section, vous allez activer et désactiver une identité attribuée au système à l’aide d’un modèle Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Activer une identité attribuée au système lors de la création d’une machine virtuelle Azure, ou sur une machine virtuelle existante

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vérifiez également que votre compte appartient à un rôle vous donnant des autorisations en écriture sur la machine virtuelle (par exemple, le rôle « Contributeur de machines virtuelles »).

2. Après le chargement du modèle dans un éditeur, localisez la ressource d’intérêt `Microsoft.Compute/virtualMachines` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.

   >[!NOTE] 
   > Cet exemple suppose que des variables telles que `vmName`, `storageAccountName` et `nicName` ont été définies dans le modèle.
   >

   ![Capture d’écran de modèle - localiser une machine virtuelle](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Pour activer l’identité attribuée au système, ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachines"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Facultatif) Ajoutez l’extension MSI de machine virtuelle en tant qu’élément `resources`. Cette étape est facultative car vous pouvez également utiliser le point de terminaison d’identité Azure IMDS (Instance Metadata Service) pour récupérer des jetons.  Utilisez la syntaxe suivante :

   >[!NOTE] 
   > L’exemple suivant suppose qu’une extension de la machine virtuelle Windows (`ManagedIdentityExtensionForWindows`) est en cours de déploiement. À la place, vous pouvez également configurer pour Linux à l’aide de `ManagedIdentityExtensionForLinux`, pour les éléments `"name"` et `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. Lorsque vous avez terminé, votre modèle doit ressembler au suivant :

   ![Capture d’écran de modèle après mise à jour](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Désactiver une identité attribuée au système à partir d’une machine virtuelle Azure

> [!NOTE]
> La désactivation de Managed Service Identity à partir d’une machine virtuelle n’est pas prise en charge actuellement. En attendant, vous pouvez basculer entre des identités attribuées au système et des identités attribuées à l’utilisateur.

Si vous avez une machine virtuelle qui ne nécessite plus d’identité MSI :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vérifiez également que votre compte appartient à un rôle vous donnant des autorisations en écriture sur la machine virtuelle (par exemple, le rôle « Contributeur de machines virtuelles »).

2. Sélectionnez le type d’identité `UserAssigned`.

## <a name="user-assigned-identity"></a>identité attribuée à l’utilisateur

Dans cette section, vous allez attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure à l’aide du modèle Azure Resource Manager.

> [!Note]
> Pour créer une identité attribuée à l’utilisateur à l’aide d’un modèle Azure Resource Manager, consultez [Créer une identité attribuée à l’utilisateur](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure

1. Sous l’élément `resources`, ajoutez l’entrée suivante pour attribuer une identité attribuée à l’utilisateur à votre machine virtuelle.  Veillez à remplacer `<USERASSIGNEDIDENTITY>` par le nom de l’identité attribuée à l’utilisateur que vous avez créée.
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITYNAME>)']"
            ]
        },
    ```
    
2. (Facultatif) Ensuite, sous l’élément `resources`, ajoutez l’entrée suivante pour attribuer l’extension d’identité gérée à votre machine virtuelle. Cette étape est facultative car vous pouvez également utiliser le point de terminaison d’identité Azure IMDS (Instance Metadata Service) pour récupérer des jetons. Utilisez la syntaxe suivante :
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3.  Quand vous avez terminé, votre modèle doit ressembler au suivant :

      ![Capture d’écran de l’identité attribuée à l’utilisateur](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Contenu connexe

- Pour une perspective plus large concernant sur l’identité du service administré, lisez la [Vue d’ensemble de l’identité du service administré](overview.md).

