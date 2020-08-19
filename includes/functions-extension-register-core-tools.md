---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031119"
---
Si vous ne pouvez pas utiliser les packs d’extensions, vous pouvez utiliser Azure Functions Core Tools localement pour installer les packs d’extensions spécifiques requis par votre projet. 

> [!NOTE]
> Pour installer manuellement des extensions avec Core Tools, le kit SDK .NET Core 2.x doit être installé.

Lorsque vous installez explicitement des extensions, un fichier de projet .NET nommé extensions.csproj est ajouté à la racine de votre projet. Ce fichier définit l’ensemble des packages NuGet requis par vos fonctions. Bien que vous puissiez utiliser les [références de package NuGet](/nuget/consume-packages/package-references-in-project-files) dans ce fichier, Core Tools vous permet d’installer des extensions sans avoir à modifier manuellement le fichier.

Il existe plusieurs façons d’utiliser les outils de base pour installer les extensions requises dans votre projet local. 

#### <a name="install-all-extensions"></a>Installer toutes les extensions 

Utilisez la commande suivante pour ajouter automatiquement tous les packages d’extensions utilisés par les liaisons dans votre projet local :

```dotnetcli
func extensions install
```
La commande lit le fichier *function.json* pour voir les packages dont vous avez besoin, les installe et regénère le projet des extensions (extensions.csproj). Il ajoute les nouvelles liaisons à la version actuelle, mais ne met pas à jour les liaisons existantes. Utilisez l’option `--force` pour mettre à jour les liaisons existantes vers la dernière version pendant les nouvelles installations.

#### <a name="install-a-specific-extension"></a>Installer une extension spécifique

Utilisez la commande suivante pour installer un package d’extension spécifique à une version spécifique, dans ce cas l’extension Storage :

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```