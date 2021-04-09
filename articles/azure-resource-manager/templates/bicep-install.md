---
title: Configurer les environnements de développement et de déploiement de Bicep
description: Comment configurer les environnements de développement et de déploiement de Bicep
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: d665a863affdec2009fc208f76b85a7f25de451d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594391"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Configurer l’environnement de développement et de déploiement de Bicep

Apprenez à configurer les environnements de développement et de déploiement de Bicep.

## <a name="development-environment"></a>Environnement de développement

Pour obtenir la meilleure expérience en matière de création de Bicep, vous avez besoin de deux composants :

- **L’extension Bicep pour Visual Studio Code**. Pour créer des fichiers Bicep, vous avez besoin d’un bon éditeur Bicep. Nous vous recommandons [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Ces outils fournissent la prise en charge linguistique et la saisie semi-automatique des ressources. Ils aident à créer et à valider les fichiers Bicep. Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **Bicep CLI**. Utilisez Bicep CLI pour compiler les fichiers Bicep en modèles ARM JSON et décompiler les modèles ARM JSON en fichiers Bicep. Pour plus d’informations, consultez [Installer Bicep CLI](#install-bicep-cli).

## <a name="deployment-environment"></a>Environnement de déploiement

Vous pouvez déployer des fichiers Bicep en utilisant Azure CLI ou Azure PowerShell. Pour Azure CLI, vous avez besoin de la version 2.20.0 ou ultérieure ; pour Azure PowerShell, vous avez besoin de la version 5.6.0 ou ultérieure. Pour les instructions d’installation, consultez :

- [Installation d'Azure PowerShell](/powershell/azure/install-az-ps)
- [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows)
- [Installer Azure CLI sur Linux](/cli/azure/install-azure-cli-linux)
- [Installer Azure CLI sur macOS](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> Actuellement, Azure CLI et Azure PowerShell ne peuvent déployer que des fichiers Bicep locaux. Pour plus d’informations sur le déploiement des fichiers Bicep à l’aide d’Azure CLI, consultez [Déployer – CLI](/deploy-cli.md#deploy-remote-template). Pour plus d’informations sur le déploiement des fichiers Bicep à l’aide d’Azure PowerShell, consultez [Déployer – PowerShell](/deploy-powershell.md#deploy-remote-template).

Après avoir installé la version prise en charge d’Azure PowerShell ou d’Azure CLI, vous pouvez déployer un fichier Bicep avec :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Installer Bicep CLI

Vous pouvez installer Bicep CLI manuellement ou en utilisant Azure CLI ou Azure PowerShell.

### <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

Avec l’installation de la version 2.20.0 ou ultérieure d’Az CLI, Bicep CLI est automatiquement installé lorsqu’une commande qui en dépend est exécutée. Par exemple, `az deployment ... -f *.bicep` ou `az bicep ...`.

Vous pouvez également installer manuellement l’interface CLI à l’aide des commandes intégrées :

```bash
az bicep install
```

Pour mettre à niveau vers la version la plus récente :

```bash
az bicep upgrade
```

Pour installer une version spécifique :

```bash
az bicep install --version v0.2.212
```

> [!NOTE]
> Az CLI installe une version distincte de Bicep CLI qui n’est pas en conflit avec d’autres installations de Bicep que vous pourriez avoir, et Az CLI n’ajoute pas Bicep à votre PATH.

Pour afficher les versions installées :

```bash
az bicep version
```

Pour répertorier toutes les versions disponibles de Bicep CLI :

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Azure PowerShell n’a pas encore la capacité d’installer Bicep CLI. Azure PowerShell (v5.6.0 ou version ultérieure) s’attend à ce que Bicep CLI soit déjà installé et disponible sous le PATH. Suivez l’une des [méthodes d’installation manuelle](#install-manually). Une fois que Bicep CLI est installé, il est appelé chaque fois qu’il est requis pour une cmdlet de déploiement. Par exemple : `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`.

### <a name="install-manually"></a>Installer manuellement

Les méthodes suivantes installent Bicep CLI et l’ajoutent à votre PATH.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>installation manuelle macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Téléchargez et exécutez le [programme d’installation Windows le plus récent](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Le programme d’installation ne nécessite pas de privilèges administratifs. Après l’installation, Bicep CLI est ajouté à votre PATH utilisateur. Fermez et rouvrez toutes les fenêtres ouvertes de l’interpréteur de commandes pour que le changement de PATH prenne effet.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manuel avec PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Installer des builds nocturnes

Si vous souhaitez essayer les dernières préversions de Bicep avant qu’elles ne soient publiées, consultez [Installer des builds nocturnes](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Ces builds en préversion sont bien plus susceptibles de contenir des bogues connus ou inconnus.

## <a name="next-steps"></a>Étapes suivantes

Commencez par le [démarrage rapide Bicep](./quickstart-create-bicep-use-visual-studio-code.md).
