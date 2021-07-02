---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Prérequis pour l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 3a3e32a0ef85ba870cfe7af9639794632549f614
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350676"
---
## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce tutoriel, vous devez d’abord satisfaire les prérequis suivants. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="get-required-resources"></a>Obtenir les ressources requises

Pour suivre ce tutoriel, installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) **version 16.5 ou ultérieure** sur votre machine de développement. Si une version antérieure est déjà installée, vous pouvez ouvrir l’application *Visual Studio Installer* sur votre ordinateur et suivre les invites pour mettre à jour votre installation.

>[!NOTE]
> Vérifiez que votre installation de Visual Studio 2019 comprend la [charge de travail de développement Azure](/dotnet/azure/configure-visual-studio) . Cette charge de travail permet à une application de publier des fonctions Azure et d’effectuer d’autres tâches de développement Azure.

Ce tutoriel est piloté par un [exemple de projet de bout en bout Azure Digital Twins écrit en C#](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Pour **obtenir l’exemple de projet** sur votre machine, suivez le lien vers l’exemple, puis sélectionnez le bouton *Parcourir le code* sous le titre. Vous accédez alors au dépôt GitHub d’exemples, que vous pouvez télécharger sous forme de fichier .zip en sélectionnant le bouton *Code*, puis *Download ZIP*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Capture d’écran du dépôt digital-twins-samples sur GitHub, mettant en évidence les étapes permettant de le télécharger sous la forme d’un fichier zip." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Cela entraîne le téléchargement d’un fichier nommé **digital-twins-samples-master.zip** sur votre machine. Décompressez le dossier et extrayez les fichiers.

### <a name="prepare-an-azure-digital-twins-instance"></a>Préparer une instance Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
