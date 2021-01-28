---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Prérequis pour l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660519"
---
## <a name="prerequisites"></a>Prérequis

Si vous ne disposez pas d’abonnement Azure, **créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** avant de commencer.

Avant de commencer, **installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), version 16.5 ou ultérieure** sur votre ordinateur de développement. Si une version antérieure est déjà installée, vous pouvez ouvrir l’application *Visual Studio Installer* sur votre ordinateur et suivre les invites pour mettre à jour votre installation.

Ce tutoriel est piloté par un exemple de projet écrit en C#. L’exemple se trouve ici : [Exemples Azure Digital Twins de bout en bout](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Pour **obtenir l’exemple de projet** sur votre machine, suivez le lien vers l’exemple, puis sélectionnez le bouton *Parcourir le code* sous le titre. Vous accédez alors au dépôt GitHub des exemples, que vous pouvez télécharger au format *.ZIP* en sélectionnant le bouton *Code*, puis *Download ZIP* (Télécharger le ZIP).

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Vue du dépôt digital-twins-samples sur GitHub. Le bouton Code est sélectionné : il ouvre une petite boîte de dialogue où le bouton Download ZIP est mis en évidence." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Ceci télécharge un dossier *.ZIP* sur votre machine sous le nom **digital-twins-samples-master.zip**. Décompressez le dossier et extrayez les fichiers.

### <a name="prepare-an-azure-digital-twins-instance"></a>Préparer une instance Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
