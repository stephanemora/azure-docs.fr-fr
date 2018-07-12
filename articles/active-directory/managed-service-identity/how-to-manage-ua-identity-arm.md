---
title: Comment créer et supprimer une identité MSI (Managed Service Identity) avec Azure Resource Manager
description: Instructions étape par étape pour créer et supprimer une identité MSI (Managed Service Identity) avec Azure Resource Manager.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: a7ddcb834b135d2177355a0523c7e99bcc599e99
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931512"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Créer, répertorier et supprimer une identité affectée par l’utilisateur avec Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous créez une identité affectée par l’utilisateur à l’aide d’Azure Resource Manager.

Il n’est pas possible de répertorier et supprimer une identité affectée par l’utilisateur avec un modèle Azure Resource Manager.  Consultez les articles suivants pour créer et énumérer une identité affectée par l’utilisateur :

- [Répertorier une identité affectée par l’utilisateur](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Supprimer une identité affectée par l’utilisateur](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

Si vous vous connectez à Azure localement ou via le portail Azure, utilisez un compte associé à l’abonnement Azure qui contient l’ordinateur virtuel. Vérifiez également que votre compte appartient à un rôle vous donnant des autorisations en écriture sur la machine virtuelle (par exemple, le rôle « Contributeur de machines virtuelles »).

## <a name="template-creation-and-editing"></a>Création et modification du modèle

Comme pour le portail Azure et le script, les modèles Azure Resource Manager offrent la possibilité de déployer des ressources nouvelles ou modifiées définies par un groupe de ressources Azure. Plusieurs options sont disponibles pour la modification du modèle et le déploiement, à la fois localement et sur le portail, y compris :

- Utiliser un [modèle personnalisé à partir de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), lequel vous permet de créer un modèle à partir de zéro, ou à partir d’un modèle commun existant ou d’un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/).
- Dériver à partir d’un groupe de ressources existant, en exportant un modèle à partir du [déploiement d’origine](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), ou à partir de l’[état actuel du déploiement](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Utilisation d’un [éditeur local JSON (VS Code, par exemple)](../../azure-resource-manager/resource-manager-create-first-template.md), puis téléchargement/déploiement à l’aide de PowerShell ou Azure CLI.
- Utilisez le [projet de groupe de ressources Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio pour créer et déployer un modèle. 

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée à l’utilisateur 

Pour créer une identité affectée par l’utilisateur, utilisez le modèle suivant. Remplacez la valeur `<USER ASSIGNED IDENTITY NAME>` avec vos propres valeurs :

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="related-content"></a>Contenu connexe

Pour plus d’informations sur la façon d’attribuer une identité affectée par l’utilisateur à une machine virtuelle Azure à l’aide d’Azure Resource Manager, consultez [Configurer une identité du service administré de machine virtuelle à l’aide d’un modèle](qs-configure-template-windows-vm.md).


 
