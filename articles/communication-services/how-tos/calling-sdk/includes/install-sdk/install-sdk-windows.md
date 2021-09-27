---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700470"
---
## <a name="setting-up"></a>Configuration

### <a name="creating-the-visual-studio-project"></a>Création du projet Visual Studio

Dans Visual Studio 2019, créez un nouveau projet `Blank App (Universal Windows)`. Après avoir entré le nom du projet, n’hésitez pas à choisir un version de Microsoft Windows SDK ultérieure à `10.0.17134`. 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>Installer le package et les dépendances avec le gestionnaire de package NuGet

Les API et les bibliothèques du Kit de développement logiciel (SDK) Appel sont accessibles au public via un package NuGet.
Les étapes suivantes illustrent comment rechercher, télécharger et installer le package NuGet du Kit de développement logiciel (SDK) Appel.

1. Ouvrez le gestionnaire de package NuGet (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`).
2. Cliquez sur `Browse`, puis saisissez `Azure.Communication.Calling` dans la zone de recherche.
3. Vérifiez que la case `Include prerelease` est cochée.
4. Cliquez sur le package `Azure.Communication.Calling`.
5. Cochez la case correspondant au projet CS sur l’onglet de droite.
6. Cliquez sur le bouton `Install`.