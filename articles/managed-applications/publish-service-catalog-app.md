---
title: Créer et publier une application managée du catalogue de services Azure | Microsoft Docs
description: Montre comment créer une application managée Azure destinée aux membres de votre organisation.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: dc86943924cd0c47c465e9d3bac4ca91b73a3ff5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112777"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Créer et publier une définition d’application gérée

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez créer et publier des[applications managées](overview.md) Azure pour les besoins des membres de votre organisation. Par exemple, un service informatique peut publier des applications managées conformes aux normes de l’organisation. Ces applications managées sont disponibles dans le catalogue de services, et non dans la Place de marché Azure.

Pour publier une application managée pour le catalogue de services, vous devez :

* Créer un modèle qui définit les ressources à déployer avec l’application managée.
* Définir les éléments d’interface utilisateur du portail lors du déploiement de l’application managée.
* Créer un package .zip qui contient les fichiers de modèles nécessaires.
* désigner l’utilisateur, le groupe ou l’application qui doit avoir accès au groupe de ressources dans l’abonnement de l’utilisateur ;
* créer la définition de l’application managée qui pointe vers le package .zip et qui demande l’accès pour l’identité.

Pour cet article, votre application managée contient uniquement un compte de stockage. Il a pour but d’illustrer les étapes de la publication d’une application managée. Pour obtenir des exemples complets, consultez [Exemples de projets pour des applications managées Azure](sample-projects.md).

Les exemples de PowerShell de cet article nécessitent la version 6.2 ou ultérieure d’Azure PowerShell. Si nécessaire, [mettez votre version à jour](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Créer le modèle de ressource

Chaque définition d’application managée contient un fichier nommé **mainTemplate.json**. Vous y définissez les ressources Azure à déployer. Le modèle n’est en rien différent d’un modèle Resource Manager normal.

Créez un fichier nommé **mainTemplate.json**. Le nom respecte la casse.

Ajoutez le code JSON suivant à votre fichier. Il définit les paramètres pour la création d’un compte de stockage et spécifie les propriétés du compte de stockage.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Enregistrez le fichier mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Créer la définition d’interface utilisateur

Le portail Azure utilise le fichier **createUiDefinition.json** afin de générer l’interface utilisateur pour les utilisateurs qui créent l’application managée. Vous déterminez la façon dont les utilisateurs fournissent une entrée pour chaque paramètre. Vous pouvez utiliser des options comme un sélecteur de liste déroulante, une zone de texte, une zone de mot de passe et d’autres outils de saisie. Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).

Créez un fichier nommé **createUiDefinition.json**. Le nom respecte la casse.

Ajoutez le code JSON suivant au fichier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Enregistrez le fichier createUiDefinition.json.

## <a name="package-the-files"></a>Empaqueter les fichiers

Ajoutez les deux fichiers à un fichier .zip nommé app.zip. Les deux fichiers doivent se trouver au niveau racine du fichier .zip. Si vous les placez dans un dossier, vous obtenez une erreur au moment de créer la définition de l’application managée qui indique que les fichiers nécessaires ne sont pas présents. 

Chargez le package à un emplacement accessible à partir de là où il peut être utilisé. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Créer un groupe d’utilisateurs ou une application Azure Active Directory

L’étape suivante consiste à sélectionner un groupe d’utilisateurs ou une application pour gérer les ressources pour le compte du client. Ce groupe d’utilisateurs ou l’application dispose d’autorisations sur le groupe de ressources managé en fonction du rôle attribué. Le rôle peut être n’importe quel rôle Contrôle d’accès en fonction du rôle (RBAC) intégré, par exemple Propriétaire ou Contributeur. Vous pouvez également autoriser un utilisateur individuel à pour gérer les ressources, mais en général, cette autorisation est attribuée à un groupe d’utilisateurs. Pour créer un groupe d’utilisateurs Active Directory, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Vous avez besoin de l’ID d’objet du groupe d’utilisateurs à utiliser pour gérer les ressources. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Obtenir l’ID de définition de rôle

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur, au groupe d’utilisateurs ou à l’application. En règle générale, vous utilisez le rôle Propriétaire, Collaborateur ou Lecteur. La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle Propriétaire :

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

Si vous ne disposez pas déjà d’un groupe de ressources pour stocker la définition de votre application managée, créez-en un maintenant :

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

À présent, créez la ressource de définition de l’application managée.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>Assurez-vous que les utilisateurs peuvent voir votre définition.

Vous avez accès à la définition de l’application managée, mais vous souhaitez vous assurer que d’autres utilisateurs de votre organisation peuvent y accéder. Accordez-leur au moins le rôle Lecteur dans la définition. Ils ont peut-être hérité de ce niveau d’accès suite à l’abonnement ou au groupe de ressources. Pour savoir qui a accès à la définition et ajouter des utilisateurs ou des groupes, consultez [Utiliser le contrôle d’accès basé sur le rôle pour gérer l’accès aux ressources de votre abonnement Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour publier votre application gérée sur la Place de marché Microsoft Azure, consultez [Applications gérées Azure sur la Place de marché](publish-marketplace-app.md).
* Pour déployer une instance de l’application gérée, consultez [Deploy service catalog app through Azure portal](deploy-service-catalog-quickstart.md) (Déployer une application de catalogue de services via le Portail Azure).
