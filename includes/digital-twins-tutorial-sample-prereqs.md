---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Prérequis pour l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622256"
---
## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce tutoriel, vous devez d’abord satisfaire les prérequis suivants. 

Si vous ne disposez pas d’abonnement Azure, **créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** avant de commencer.

### <a name="get-required-resources"></a>Obtenir les ressources requises

Pour suivre ce tutoriel, **installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) version 16.5 ou ultérieure** sur votre ordinateur de développement. Si une version antérieure est déjà installée, vous pouvez ouvrir l’application *Visual Studio Installer* sur votre ordinateur et suivre les invites pour mettre à jour votre installation.

>[!NOTE]
> Vérifiez que votre installation de Visual Studio 2019 comprend la **[charge de travail de développement Azure](/dotnet/azure/configure-visual-studio)** . Cette charge de travail permet à une application de publier des fonctions Azure et d’effectuer d’autres tâches de développement Azure.

Ce tutoriel est piloté par un exemple de projet écrit en C#. L’exemple se trouve ici : [Exemples Azure Digital Twins de bout en bout](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Pour **obtenir l’exemple de projet** sur votre machine, suivez le lien vers l’exemple, puis sélectionnez le bouton *Parcourir le code* sous le titre. Vous accédez alors au dépôt GitHub des exemples, que vous pouvez télécharger au format *.ZIP* en sélectionnant le bouton *Code*, puis *Download ZIP* (Télécharger le ZIP).

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Vue du dépôt digital-twins-samples sur GitHub. La sélection du bouton Code entraîne l’ouverture d’une petite boîte de dialogue dans laquelle le bouton Download ZIP est mis en évidence." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Ceci télécharge un dossier *.ZIP* sur votre machine sous le nom **digital-twins-samples-master.zip**. Décompressez le dossier et extrayez les fichiers.

### <a name="prepare-an-azure-digital-twins-instance"></a>Préparer une instance Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
