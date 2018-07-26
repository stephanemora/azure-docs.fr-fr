---
title: Configurer l’identité du service administré sur un groupe de machines virtuelles identiques Azure à l’aide d’un modèle
description: Instructions détaillées sur la configuration d’une identité du service administré (MSI) sur un groupe de machine virtuelle identique Azure, à l’aide d’un modèle Azure Resource Manager.
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
ms.openlocfilehash: ab3982c85cfb008bde08495f8cb8aa86d066d8c0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114852"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Configurer une identité MSI (Managed Service Identity) de groupe de machines virtuelles identiques à l’aide d’un modèle

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment effectuer les opérations Managed Service Identity suivantes sur un groupe de machines virtuelles identiques Azure, à l’aide du modèle de déploiement Azure Resource Manager :
- Activer et désactiver l’identité attribuée au système sur un groupe de machines virtuelles identiques Azure
- Ajouter et supprimer une identité attribuée par l’utilisateur sur un groupe de machines virtuelles identiques Azure

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Comme pour le portail Azure et le script, les modèles [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

   - Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
   - Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
   - Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle.  

Quelle que soit l’option choisie, la syntaxe de modèle est identique lors du déploiement initial et lors du redéploiement. L’activation du MSI est effectuée de la même manière sur une machine virtuelle nouvelle ou non. De plus, par défaut, Azure Resource Manager effectue une [mise à jour incrémentielle](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) au niveau des déploiements.

## <a name="system-assigned-identity"></a>Identité attribuée au système

Dans cette section, vous allez activer et désactiver une identité attribuée au système à l’aide d’un modèle Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Activer une identité attribuée au système lors de la création d’un groupe de machines virtuelles identiques Azure, ou sur un groupe de machines virtuelles identiques Azure existant

1. Chargez le modèle dans un éditeur et localisez la ressource d’intérêt `Microsoft.Compute/virtualMachineScaleSets` dans la section `resources`. La vôtre peut différer légèrement de la capture d’écran suivante, selon l’éditeur que vous utilisez et si vous modifiez un modèle pour un déploiement nouveau ou existant.
   
   ![Capture d’écran de modèle - localiser une machine virtuelle](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Pour activer l’identité attribuée au système, ajoutez la propriété `"identity"` au même niveau que la propriété `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Utilisez la syntaxe suivante :

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Facultatif) Ajoutez l’extension MSI de groupe de machines virtuelles identiques en tant qu’élément `extensionsProfile`. Cette étape est facultative car vous pouvez également utiliser l’identité Azure IMDS (Instance Metadata Service) pour récupérer des jetons.  Utilisez la syntaxe suivante :

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

> [!NOTE]
> La désactivation de Managed Service Identity à partir d’une machine virtuelle n’est pas prise en charge actuellement. En attendant, vous pouvez basculer entre des identités attribuées au système et des identités attribuées à l’utilisateur.

Si vous avez un groupe de machines virtuelles identiques qui n’a plus besoin d’identité attribuée au système mais qui a toujours besoin d’identités attribuées à l’utilisateur :

- Chargez le modèle dans un éditeur et sélectionnez le type d’identité `'UserAssigned'`.

## <a name="user-assigned-identity"></a>Identité attribuée à l’utilisateur

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

## <a name="next-steps"></a>Étapes suivantes

- Pour une perspective plus large concernant sur l’identité du service administré, lisez la [Vue d’ensemble de l’identité du service administré](overview.md).

