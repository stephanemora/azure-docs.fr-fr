---
title: Configurer des environnements de développement et de déploiement Bicep
description: Comment configurer les environnements de développement et de déploiement de Bicep
ms.topic: conceptual
ms.date: 08/26/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c95c05923d9685232c50d694f2b858e2de9e4776
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099562"
---
# <a name="install-bicep-tools"></a>Installer les outils Bicep

Vérifions que votre environnement est configuré pour le développement et le déploiement de fichiers Bicep.

## <a name="development-environment"></a>Environnement de développement

Pour créer des fichiers Bicep, vous avez besoin d’un bon éditeur Bicep. Nous recommandons les actions suivantes :

- **Visual Studio Code** - Si vous ne disposez pas déjà de Visual Studio Code, [installez-le](https://code.visualstudio.com/).
- **L’extension Bicep pour Visual Studio Code**.  Visual Studio Code avec l’extension Bicep fournit la prise en charge du langage et la saisie semi-automatique des ressources. L’extension vous aide à créer et à valider des fichiers Bicep.

  Pour installer l’extension, recherchez *bicep* sous l’onglet **Extensions** ou sur [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).

  Sélectionnez **Installer**.

  :::image type="content" source="./media/install/install-extension.png" alt-text="Installer l’extension Bicep":::

Pour vérifier que vous avez installé l’extension, ouvrez n’importe quel fichier ayant l’extension `.bicep`. Le mode de langage indiqué en bas à droite doit passer à **Bicep**.

:::image type="content" source="./media/install/language-mode.png" alt-text="Mode de langage Bicep":::

## <a name="deployment-environment"></a>Environnement de déploiement

Le moyen le plus simple de disposer des commandes nécessaires pour déployer un fichier Bicep consiste à installer la dernière version d’Azure CLI. Vous pouvez également utiliser PowerShell, mais cela nécessite une installation supplémentaire.

- [Azure CLI](#azure-cli)
- [Azure PowerShell](#azure-powershell)
- [Installer manuellement](#install-manually)

### <a name="azure-cli"></a>Azure CLI

Vous devez avoir installé Azure CLI 2.20.0 ou une version ultérieure. Pour installer ou mettre à jour Azure CLI, consultez :

- [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows)
- [Installer Azure CLI sur Linux](/cli/azure/install-azure-cli-linux)
- [Installer Azure CLI sur macOS](/cli/azure/install-azure-cli-macos)

Pour vérifier votre version actuelle, exécutez la commande suivante :

```azurecli
az --version
```

Vous disposez maintenant de tout ce dont vous avez besoin pour [déployer](deploy-cli.md) et [décompiler](decompile.md) des fichiers Bicep. En effet, Azure CLI 2.20.0 (ou version ultérieure) installe automatiquement l’interface CLI Bicep quand une commande qui en a besoin est exécutée.

Pour démarrer manuellement l’installation de l’interface CLI Bicep, utilisez la commande :

```azurecli
az bicep install
```

Pour mettre à niveau l’interface vers la version la plus récente, utilisez la commande suivante :

```azurecli
az bicep upgrade
```

Pour valider l’installation, utilisez la commande suivante :

```azurecli
az bicep version
```

Pour connaître plus de commandes, consultez [CLI Bicep](bicep-cli.md).

> [!IMPORTANT]
> L’interface Azure CLI installe une instance autonome de l’interface CLI Bicep. Cette instance n’entre en conflit avec aucune des versions que vous auriez installées manuellement. Azure CLI n’ajoute pas l’interface CLI Bicep à votre PATH.

#### <a name="install-on-an-air-gapped-cloud"></a>Installer sur un Cloud Air

Pour installer Bicep CLI dans un environnement à couverture aérienne, vous devez télécharger manuellement l'exécutable de Bicep CLI et l'enregistrer à un certain endroit.

- **Linux**

    1. Téléchargez **bicep-Linux-x64** à partir de la [page de publication bicep](https://github.com/Azure/bicep/releases/latest/) dans un environnement sans air.
    1. Copiez l’exécutable dans le répertoire **$Home/.Azure/bin** sur une machine à airer.

- **macOS**

    1. Téléchargez **bicep-Linux-x64** à partir de la [page de publication bicep](https://github.com/Azure/bicep/releases/latest/) dans un environnement sans air.
    1. Copiez l’exécutable dans le répertoire **$Home/.Azure/bin** sur une machine à airer.

- **Windows**

    1. Téléchargez **bicep-Linux-x64** à partir de la [page de publication bicep](https://github.com/Azure/bicep/releases/latest/) dans un environnement sans air.
    1. Copiez l'exécutable dans le répertoire **%UserProfile%/.azure/bin** sur une machine à couverture aérienne.

Notez que les commandes `bicep install` et `bicep upgrade` ne fonctionnent pas dans un environnement air.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 5.6.0 (ou une version ultérieure) doit être installé. Pour effectuer une mise à jour ou une installation, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps).

Azure PowerShell n’installe pas automatiquement l’interface CLI Bicep. En fait, vous devez [installer manuellement l’interface CLI Bicep](#install-manually).

> [!IMPORTANT]
> L’instance autonome de l’interface CLI Bicep installée par Azure CLI n’est pas disponible pour les commandes PowerShell. Les déploiements Azure PowerShell échouent si vous n’avez pas installé manuellement l’interface CLI Bicep.

Quand vous installez manuellement l’interface CLI Bicep, exécutez les commandes Bicep avec la syntaxe `bicep` et non la syntaxe `az bicep` d’Azure CLI.

Pour déployer des fichiers Bicep, utilisez la version 0.3.1 de l’interface CLI Bicep ou une version ultérieure. Pour vérifier votre version de l’interface CLI Bicep, exécutez la commande suivante :

```cmd
bicep --version
```

### <a name="install-manually"></a>Installer manuellement

Les méthodes suivantes installent Bicep CLI et l’ajoutent à votre PATH. Vous devez effectuer une installation manuelle si vous envisagez d’utiliser un autre outil qu’Azure CLI.

- [Linux](#linux)
- [macOS](#macos)
- [Windows](#windows)

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

> [!NOTE]
> Pour les distributions Linux légères comme [Alpine](https://alpinelinux.org/), utilisez **bicep-linux-musl-x64** au lieu de **bicep-linux-x64** dans le script précédent.

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="via-bash"></a>Avec BASH

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

### <a name="install-the-nightly-builds"></a>Installer des builds nocturnes

Si vous souhaitez essayer les dernières préversions de Bicep avant qu’elles ne soient publiées, consultez [Installer des builds nocturnes](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Ces builds en préversion sont bien plus susceptibles de contenir des bogues connus ou inconnus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
