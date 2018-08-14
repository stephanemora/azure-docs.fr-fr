---
title: Guide pratique pour configurer Managed Service Identity sur une machine virtuelle Azure avec un modèle
description: Instructions détaillées pour configurer une identité de service managée sur une machine virtuelle Azure à l’aide d’un modèle Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 79b499f8063e5c15f76d89182955cbd90fb1039f
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629308"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurer une identité du service administré de machine virtuelle à l’aide d’un modèle

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment effectuer les opérations Managed Service Identity suivantes sur une machine virtuelle Azure, à l’aide du modèle de déploiement Azure Resource Manager :

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions des rôles suivants :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour créer une machine virtuelle puis activer et supprimer l’identité managée affectée par le système et/ou l’utilisateur sur une machine virtuelle Azure.
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour attribuer et supprimer une identité affectée par l’utilisateur depuis et vers une machine virtuelle.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Comme pour le portail Azure et le script, les modèles [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. L’activation d’une identité attribuée au système ou à l’utilisateur à une machine virtuelle nouvelle ou existante s’effectuent de la même manière. En outre, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../../azure-resource-manager/deployment-modes.md) au niveau des déploiements.

## <a name="system-assigned-identity"></a>identité attribuée au système

Dans cette section, vous allez activer et désactiver une identité attribuée au système à l’aide d’un modèle Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Activer une identité affectée par le système pendant la création d’une machine virtuelle Azure ou sur une machine virtuelle existante

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel.

2. Après le chargement du modèle dans un éditeur, localisez la ressource d’intérêt `Microsoft.Compute/virtualMachines` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.

   >[!NOTE] 
   > Cet exemple suppose que des variables telles que `vmName`, `storageAccountName` et `nicName` ont été définies dans le modèle.
   >

   ![Capture d’écran de modèle - localiser une machine virtuelle](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Pour activer l’identité attribuée au système, ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachines"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Facultatif) Ajouter l’extension Managed Service Identity pour machine virtuelle en tant qu’élément `resources`. Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.  Utilisez la syntaxe suivante :

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

   ![Capture d’écran de modèle après mise à jour](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Attribuer un rôle à l’identité affectée par le système de la machine virtuelle

Une fois que vous avez activé l’identité affectée par le système sur votre machine virtuelle, vous pouvez lui accorder un rôle, comme l’accès en **lecture** sur le groupe de ressources dans lequel elle a été créée.

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel.
 
2. Chargez le modèle dans un [éditeur](#azure-resource-manager-templates) et ajoutez les informations suivantes pour donner à votre machine virtuelle un accès en **lecture** sur le groupe de ressources dans lequel elle a été créée.  Votre structure de modèle peut varier en fonction de l’éditeur et du modèle de déploiement que vous choisissez.
   
   Sous la section `parameters`, ajoutez ce qui suit :

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Sous la section `variables`, ajoutez ce qui suit :

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Sous la section `resources`, ajoutez ce qui suit :

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Désactiver une identité attribuée au système à partir d’une machine virtuelle Azure

Si vous avez une machine virtuelle qui ne nécessite plus d’identité MSI :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel.

2. Chargez le modèle dans un [éditeur](#azure-resource-manager-templates) et localisez la ressource `Microsoft.Compute/virtualMachines` qui vous intéresse dans la section `resources`. Si votre machine virtuelle dispose uniquement de l’identité affectée par le système, vous pouvez la désactiver en remplaçant le type d’identité par `None`.  Si votre machine virtuelle a des identités affectées par le système et l’utilisateur, supprimez `SystemAssigned` dans le type d’identité et conservez `UserAssigned` avec le tableau `identityIds` des identités affectées par l’utilisateur.  L’exemple suivant montre comment supprimer une identité affectée par le système sur une machine virtuelle sans identité affectée par l’utilisateur :
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Dans cette section, vous allez attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure à l’aide du modèle Azure Resource Manager.

> [!Note]
> Pour créer une identité attribuée à l’utilisateur à l’aide d’un modèle Azure Resource Manager, consultez [Créer une identité attribuée à l’utilisateur](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure

1. Sous l’élément `resources`, ajoutez l’entrée suivante pour attribuer une identité attribuée à l’utilisateur à votre machine virtuelle.  Veillez à remplacer `<USERASSIGNEDIDENTITY>` par le nom de l’identité attribuée à l’utilisateur que vous avez créée.
   
   > [!Important]
   > La valeur `<USERASSIGNEDIDENTITYNAME>` indiquée dans l’exemple suivant doit être stockée dans une variable.  En outre, pour l’implémentation actuellement prise en charge de l’affectation d’identités attribuées par l’utilisateur à une machine virtuelle dans un modèle Resource Manager, la version d’API doit correspondre à celle de l’exemple suivant.
    
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (Facultatif) Ensuite, sous l’élément `resources`, ajoutez l’entrée suivante pour attribuer l’extension d’identité gérée à votre machine virtuelle. Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons. Utilisez la syntaxe suivante :
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

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Supprimer d’une machine virtuelle Azure l’identité affectée par l’utilisateur

Si vous avez une machine virtuelle qui ne nécessite plus d’identité MSI :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel.

2. Chargez le modèle dans un [éditeur](#azure-resource-manager-templates) et localisez la ressource `Microsoft.Compute/virtualMachines` qui vous intéresse dans la section `resources`. Si votre machine virtuelle dispose uniquement de l’identité affectée par l’utilisateur, vous pouvez la désactiver en remplaçant le type d’identité par `None`.  Si votre machine virtuelle dispose à la fois des identités affectées par le système et par l’utilisateur, et que vous voulez conserver l’identité affectée par le système, supprimez `UserAssigned` dans le type d’identité avec le tableau `identityIds` des identités affectées par l’utilisateur.
    
   Pour supprimer d’une machine virtuelle une seule identité affectée par l’utilisateur, supprimez-la du tableau `identityIds`.
   
   L’exemple suivant montre comment supprimer d’une machine virtuelle toutes les identités affectées par l’utilisateur et sans identité affectée par le système :
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="related-content"></a>Contenu connexe

- Pour élargir votre connaissance sur Managed Service Identity, lisez la [Vue d’ensemble de Managed Service Identity](overview.md).

