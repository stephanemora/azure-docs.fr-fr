---
title: Configurer l’environnement de développement pour les scripts de déploiement dans les modèles | Microsoft Docs
description: Configurez l’environnement de développement pour les scripts de déploiement dans des modèles Azure Resource Manager (modèles ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a7c60ab9b3c9490c7731b3011ea9feb7ee0c02f
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316458"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Configurer l’environnement de développement pour les scripts de déploiement dans des modèles ARM

Découvrez comment créer un environnement de développement pour développer et tester des scripts de déploiement de modèle ARM avec une image de script de déploiement. Vous pouvez soit créer une [instance de conteneur Azure](../../container-instances/container-instances-overview.md), soit utiliser [Docker](https://docs.docker.com/get-docker/). Les deux options sont décrites dans cet article.

## <a name="prerequisites"></a>Prérequis

### <a name="azure-powershell-container"></a>Conteneur Azure PowerShell

Si vous n’avez pas de script de déploiement Azure PowerShell, vous pouvez créer un fichier *hello.ps1* en utilisant le contenu suivant :

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Conteneur Azure CLI

Pour une image conteneur Azure CLI, vous pouvez créer un fichier *hello.sh* en utilisant le contenu suivant :

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Lorsque vous exécutez un script de déploiement Azure CLI, une variable d’environnement appelée `AZ_SCRIPTS_OUTPUT_PATH` stocke l’emplacement du fichier de sortie du script. La variable d’environnement n’est pas disponible dans le conteneur de l’environnement de développement. Pour plus d’informations sur l’utilisation des sorties Azure CLI, consultez [Travailler avec les sorties du script CLI](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Utiliser une instance de conteneur Azure PowerShell

Pour créer vos scripts sur votre ordinateur, vous devez créer un compte de stockage et monter celui-ci dans l’instance de conteneur. Cela vous permettra de charger votre script dans le compte de stockage et d’exécuter le script sur l’instance de conteneur.

> [!NOTE]
> Le compte de stockage que vous créez pour tester votre script n’est pas le même que celui qu’utilise le service de script de déploiement pour exécuter le script. Le service de script de déploiement crée un nom unique sous forme de partage de fichiers à chaque exécution.

### <a name="create-an-azure-powershell-container-instance"></a>Créer une instance de conteneur Azure PowerShell

Le modèle Azure Resource Manager (modèle ARM) suivant crée une instance de conteneur et un partage de fichiers, puis monte ce dernier sur l’image conteneur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

La valeur par défaut du chemin de montage est `/mnt/azscripts/azscriptinput`. Il s’agit du chemin dans l’instance de conteneur où il est monté dans le partage de fichiers.

L’image conteneur par défaut spécifiée dans le modèle est **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2**. Consultez la liste de toutes les [versions d’Azure PowerShell prises en charge](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

Le modèle interrompt l’instance de conteneur après 1 800 secondes. Il faut compter 30 minutes avant que l’instance de conteneur passe à l’état terminé et que la session prenne fin.

Pour déployer le modèle, procédez comme suit :

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Charger le script de déploiement

Chargez votre script de déploiement dans le compte de stockage. Voici un exemple de script PowerShell :

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Vous pouvez aussi charger le fichier à l’aide du portail Azure ou d’Azure CLI.

### <a name="test-the-deployment-script"></a>Tester le script de déploiement

1. Dans le portail Azure, ouvrez le groupe de ressources dans lequel vous avez déployé l’instance de conteneur et le compte de stockage.
2. Ouvrez le groupe de conteneurs. Par défaut, le nom du groupe de conteneurs est le nom du projet auquel est ajouté « *cg* ». L’instance de conteneur est à l’état **En cours d’exécution**.
3. Dans le menu des ressources, sélectionnez **Conteneurs**. Le nom de l’instance de conteneur est le nom du projet auquel est ajouté « *container* ».

    ![Capture d’écran du script de déploiement Connecter l’instance de conteneur dans le portail Azure.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Sélectionnez **Connecter**, puis **Connecter**. Si vous ne pouvez pas vous connecter à l’instance de conteneur, redémarrez le groupe de conteneurs, puis réessayez.
5. Dans le volet de la console, exécutez la commande suivante :

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    La sortie est **Hello John Dole**.

    ![Capture d’écran du script de déploiement Connecter l’instance de conteneur Tester la sortie dans la console.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Utiliser une instance de conteneur Azure CLI

Pour créer vos scripts sur votre ordinateur, créez un compte de stockage et montez-le sur l’instance de conteneur. Vous pouvez ensuite charger votre script dans le compte de stockage et exécuter le script sur l’instance de conteneur.

> [!NOTE]
> Le compte de stockage que vous créez pour tester votre script n’est pas le même que celui qu’utilise le service de script de déploiement pour exécuter le script. Le service de script de déploiement crée un nom unique sous forme de partage de fichiers à chaque exécution.

### <a name="create-an-azure-cli-container-instance"></a>Créer une instance de conteneur Azure CLI

Le modèle ARM suivant crée une instance de conteneur et un partage de fichiers, puis monte ce dernier sur l’image conteneur :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

La valeur par défaut du chemin de montage est `/mnt/azscripts/azscriptinput`. Il s’agit du chemin dans l’instance de conteneur où il est monté dans le partage de fichiers.

L’image conteneur par défaut spécifiée dans le modèle est **mcr.microsoft.com/azure-cli:2.9.1**. Consultez la liste des versions de [Azure CLI prises en charge](https://mcr.microsoft.com/v2/azure-cli/tags/list).

> [!IMPORTANT]
> Le script de déploiement utilise les images CLI disponibles dans Microsoft Container Registry (MCR). Il faut environ un mois pour certifier une image CLI pour un script de déploiement. N’utilisez pas les versions de l’interface CLI qui ont été publiées il y a moins de 30 jours. Pour trouver les dates de publication des images, consultez les [Notes de publication d’Azure CLI](/cli/azure/release-notes-azure-cli). Si vous utilisez une version non prise en charge, le message d’erreur répertorie les versions prises en charge.

Le modèle interrompt l’instance de conteneur après 1 800 secondes. Il faut compter 30 minutes avant que l’instance de conteneur passe à l’état terminé et que la session prenne fin.

Pour déployer le modèle, procédez comme suit :

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Charger le script de déploiement

Chargez votre script de déploiement dans le compte de stockage. Voici un exemple PowerShell :

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Vous pouvez aussi charger le fichier à l’aide du portail Azure ou d’Azure CLI.

### <a name="test-the-deployment-script"></a>Tester le script de déploiement

1. Dans le portail Azure, ouvrez le groupe de ressources dans lequel vous avez déployé l’instance de conteneur et le compte de stockage.
1. Ouvrez le groupe de conteneurs. Par défaut, le nom du groupe de conteneurs est le nom du projet auquel est ajouté « *cg* ». L’instance de conteneur est affichée à l’état **En cours d’exécution**.
1. Dans le menu des ressources, sélectionnez **Conteneurs**. Le nom de l’instance de conteneur est le nom du projet auquel est ajouté « *container* ».

    ![script de déploiement connecter instance de conteneur](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Sélectionnez **Connecter**, puis **Connecter**. Si vous ne pouvez pas vous connecter à l’instance de conteneur, redémarrez le groupe de conteneurs, puis réessayez.
1. Dans le volet de la console, exécutez la commande suivante :

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    La sortie est **Hello John Dole**.

    ![script de déploiement instance de conteneur test](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Utiliser Docker

Vous pouvez utiliser une image conteneur Docker préconfigurée comme environnement de développement de script de déploiement. Pour installer le Docker, consultez [Obtenir le Docker](https://docs.docker.com/get-docker/).
Vous devez aussi configurer le partage de fichiers pour monter le répertoire, qui contient les scripts de déploiement dans le conteneur Docker.

1. Extrayez l’image conteneur du script de déploiement pour la mettre sur l’ordinateur local :

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    L’exemple utilise la version 4.3.0 de PowerShell.

    Pour extraire une image CLI de MCR :

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Cet exemple utilise la version 2.0.80 de CLI. Le script de déploiement utilise les images conteneurs CLI par défaut trouvées [ici](https://hub.docker.com/_/microsoft-azure-cli).

1. Exécutez l’image Docker localement.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Remplacez **&lt;host driver letter>** (lettre de lecteur hôte) et **&lt;host directory name>** (nom de répertoire hôte) par un dossier existant sur le lecteur partagé. Le dossier est alors mappé au dossier _/data_ dans le conteneur. Par exemple, pour mapper _D:\docker_ :

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-it** indique de conserver l’image conteneur active.

    Exemple CLI :

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. La capture d’écran suivante illustre comment exécuter un script PowerShell, étant donné que vous avez un fichier *helloworld.ps1* dans le dossier partagé.

    ![Commande Docker pour un script de déploiement de modèle Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Dès lors que le script a été testé avec succès, vous pouvez l’utiliser en tant que script de déploiement dans vos modèles.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser des scripts de déploiement. Pour suivre un tutoriel sur les scripts de déploiement :

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser des scripts de déploiement dans des modèles ARM](./template-tutorial-deployment-script.md)
