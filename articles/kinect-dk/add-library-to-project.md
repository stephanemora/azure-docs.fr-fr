---
title: Ajouter une bibliothèque Azure Kinect à votre projet Visual Studio
description: Découvrez comment ajouter le package NuGet Azure Kinect à votre projet Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, capteur, kit de développement logiciel (sdk), visual studio 2017, visual studio 2019, nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276496"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Ajouter une bibliothèque Azure Kinect à votre projet Visual Studio

Cet article vous guide tout au long du processus d’ajout d’un package NuGet Azure Kinect à votre projet Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Installer le package NuGet Azure Kinect

Pour installer le package NuGet Azure Kinect :

1. Vous trouverez des instructions détaillées sur l’installation d’un package NuGet dans Visual Studio dans [Démarrage rapide : Installer et utiliser un package dans Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Pour ajouter le package, vous pouvez utiliser l’interface utilisateur du gestionnaire de package en cliquant avec le bouton droit sur références et en sélectionnant Gérer les packages NuGet dans l’Explorateur de solutions.
3. Choisissez [nuget.org](https://www.nuget.org) comme source du package, sélectionnez l’onglet Parcourir, puis recherchez `Microsoft.Azure.Kinect.Sensor`.
4. Sélectionnez ce package dans la liste, puis installez-le.

## <a name="use-azure-kinect-nuget-package"></a>Utiliser le package NuGet Azure Kinect

Une fois le package ajouté, ajoutez le fichier d’en-tête inclus au code source, par exemple :

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Vous êtes maintenant prêt à créer votre première application](build-first-app.md)
