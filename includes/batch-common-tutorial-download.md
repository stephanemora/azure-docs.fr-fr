---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279591"
---
### <a name="retrieve-output-files"></a>Récupérer les fichiers de sortie

Vous pouvez utiliser le portail Azure pour télécharger les fichiers de sortie MP3 générés par les tâches ffmpeg. 

1. Cliquez sur **Tous les services** > **Comptes de stockage**, puis sur le nom de votre compte de stockage.
2. Cliquez sur **BLOB** > *sortie*.
3. Cliquez avec le bouton droit sur l’un des fichiers de sortie MP3 puis cliquez sur **Télécharger**. Suivez les invites dans votre navigateur pour ouvrir ou enregistrer le fichier.

![Télécharger le fichier de sortie](./media/batch-common-tutorial-download/download.png)

Même si ce n’est pas montré dans cet exemple, vous pouvez aussi télécharger les fichiers par programmation depuis les nœuds de calcul ou depuis le conteneur de stockage.
