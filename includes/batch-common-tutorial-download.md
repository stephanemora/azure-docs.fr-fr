---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67177413"
---
### <a name="retrieve-output-files"></a>Récupérer les fichiers de sortie

Vous pouvez utiliser le portail Azure pour télécharger les fichiers de sortie MP3 générés par les tâches ffmpeg. 

1. Cliquez sur **Tous les services** > **Comptes de stockage**, puis sur le nom de votre compte de stockage.
2. Cliquez sur **BLOB** > *sortie*.
3. Cliquez avec le bouton droit sur l’un des fichiers de sortie MP3 puis cliquez sur **Télécharger**. Suivez les invites dans votre navigateur pour ouvrir ou enregistrer le fichier.

![Télécharger le fichier de sortie](./media/batch-common-tutorial-download/download.png)

Même si ce n’est pas montré dans cet exemple, vous pouvez aussi télécharger les fichiers par programmation depuis les nœuds de calcul ou depuis le conteneur de stockage.
