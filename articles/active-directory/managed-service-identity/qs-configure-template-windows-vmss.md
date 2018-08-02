---
title: Configurer Managed Service Identity sur un groupe de machines virtuelles identiques Azure au moyen d’un modèle
description: Instructions détaillées sur la configuration d’une identité de service managée pour un groupe de machines virtuelles identiques Azure, à l’aide d’un modèle Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 562bf5e5239114a8dad16727089f94f378db82ff
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258862"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Configurer Managed Service Identity sur un groupe de machines virtuelles identiques au moyen d’un modèle

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity fournit des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous découvrez comment effectuer les opérations Managed Service Identity suivantes sur un groupe de machines virtuelles identiques Azure, à l’aide du modèle de déploiement Azure Resource Manager :
- Activer et désactiver une identité affectée par le système sur un groupe de machines virtuelles identiques Azure
- Ajouter et supprimer une identité affectée par l’utilisateur sur un groupe de machines virtuelles identiques Azure

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions de rôles suivants :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour créer un groupe de machines virtuelles identiques, puis activer et supprimer l’identité managée affectée par le système et/ou par l’utilisateur dans un groupe de machines virtuelles identiques.
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator), pour attribuer et supprimer une identité affectée par l’utilisateur depuis et vers un groupe de machines virtuelles identiques.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Comme pour le portail Azure et le script, les modèles [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. L’activation de Managed Service Identity sur une machine virtuelle nouvelle ou existante s’effectue de la même manière. De plus, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements.

## <a name="system-assigned-identity"></a>Identité attribuée au système

Dans cette section, vous allez activer et désactiver une identité attribuée au système à l’aide d’un modèle Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-or-an-existing-azure-virtual-machine-scale-set"></a>Activer une identité affectée par le système sur un groupe de machines virtuelles identiques Azure existant

1. Chargez le modèle dans un éditeur et localisez la ressource d’intérêt `Microsoft.Compute/virtualMachineScaleSets` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.
   
   ![Capture d’écran de modèle - localiser une machine virtuelle](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Pour activer l’identité attribuée au système, ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Facultatif) Ajoutez l’extension Managed Service Identity de groupe de machines virtuelles identiques en tant qu’élément `extensionsProfile`. Cette étape est facultative car vous pouvez également utiliser l’identité Azure IMDS (Instance Metadata Service) pour récupérer des jetons.  Utilisez la syntaxe suivante :

   >[!NOTE] 
   > L’exemple suivant suppose qu’une extension du groupe de machines virtuelles identiques Windows (`ManagedIdentityExtensionForWindows`) est en cours de déploiement. À la place, vous pouvez également configurer pour Linux à l’aide de `ManagedIdentityExtensionForLinux`, pour les éléments `"name"` et `"type"`.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

4. Lorsque vous avez terminé, votre modèle doit ressembler au suivant :

   ![Capture d’écran de modèle après mise à jour](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Désactiver une identité attribuée au système à partir d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité de service managée :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Chargez le modèle dans un [éditeur](#azure-resource-manager-templates) et localisez la ressource `Microsoft.Compute/virtualMachineScaleSets` qui vous intéresse dans la section `resources`. Si vous avez un groupe de machines virtuelles identiques qui n’est doté que de l’identité affectée par le système, vous pouvez désactiver cette identité en remplaçant le type d’identité par `None`.  Si votre groupe de machines virtuelles identiques dispose des identités affectées par le système et par l’utilisateur, supprimez `SystemAssigned` dans le type d’identité et conservez `UserAssigned` avec le tableau `identityIds` des identités affectées par l’utilisateur.  L’exemple suivant montre comment supprimer d’un groupe de machines virtuelles identiques une identité affectée par le système, sans avoir d’identité affectée par l’utilisateur :
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Dans cette section, vous allez attribuer une identité attribuée à l’utilisateur à un groupe de machines virtuelles identiques Azure à l’aide du modèle Azure Resource Manager.

> [!Note]
> Pour créer une identité attribuée à l’utilisateur à l’aide d’un modèle Azure Resource Manager, consultez [Créer une identité attribuée à l’utilisateur](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Attribuer une identité attribuée à l’utilisateur à un groupe de machines virtuelles identiques Azure

1. Sous l’élément `resources`, ajoutez l’entrée suivante pour attribuer une identité attribuée à l’utilisateur à votre groupe de machines virtuelles identiques.  Veillez à remplacer `<USERASSIGNEDIDENTITY>` par le nom de l’identité attribuée à l’utilisateur que vous avez créée.

   > [!Important]
   > La valeur `<USERASSIGNEDIDENTITYNAME>` indiquée dans l’exemple suivant doit être stockée dans une variable.  En outre, pour l’implémentation actuellement prise en charge de l’affectation d’identités attribuées par l’utilisateur à une machine virtuelle dans un modèle Resource Manager, la version d’API doit correspondre à celle de l’exemple suivant. 

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
            ]
        }

    }
    ```

2. (Facultatif) Ajoutez l’entrée suivante sous l’élément `extensionProfile` pour attribuer l’extension d’identité gérée à votre groupe de machines virtuelles identiques. Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons. Utilisez la syntaxe suivante :
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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

3.  Quand vous avez terminé, votre modèle doit ressembler au suivant :
   
      ![Capture d’écran de l’identité attribuée à l’utilisateur](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Supprimer d’un groupe de machines virtuelles identiques Azure l’identité attribuée par l’utilisateur

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité de service managée :

1. Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Chargez le modèle dans un [éditeur](#azure-resource-manager-templates) et localisez la ressource `Microsoft.Compute/virtualMachineScaleSets` qui vous intéresse dans la section `resources`. Si votre groupe de machines virtuelles identiques dispose uniquement de l’identité affectée par l’utilisateur, vous pouvez désactiver celle-ci en remplaçant le type d’identité par `None`.  Si votre groupe de machines virtuelles identiques est doté des deux identités, l’une affectée par le système et l’autre affectée par l’utilisateur, et que vous souhaitez conserver la première, supprimez `UserAssigned` du type d’identité avec le tableau `identityIds` des identités affectées par l’utilisateur.
    
   Pour supprimer d’un groupe de machines virtuelles identiques une seule identité affectée par l’utilisateur, supprimez-la du tableau `identityIds`.
   
   L’exemple suivant montre comment supprimer d’un groupe de machines virtuelles identiques toutes les identités affectées par l’utilisateur sans avoir aucune identité affectée par le système :
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour élargir votre connaissance sur Managed Service Identity, lisez la [Vue d’ensemble de Managed Service Identity](overview.md).

