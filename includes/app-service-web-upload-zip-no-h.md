---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "58115388"
---
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Groupes de ressources** > **cloud-shell-storage-\<votre_région>** > **\<nom_compte_stockage>**.

![Rechercher un compte de stockage Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

Sur la page **Vue d’ensemble** du compte de stockage, sélectionnez **Fichiers**.

Sélectionnez le partage de fichiers généré automatiquement, puis sélectionnez **Télécharger**. Ce partage de fichiers est monté dans Cloud Shell en tant que `clouddrive`.

![Rechercher le bouton Télécharger](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Cliquez sur le sélecteur de fichiers, sélectionnez votre fichier ZIP, puis cliquez sur **Télécharger**. 

Dans l’interface Cloud Shell, utilisez `ls` pour vérifier que le fichier ZIP téléchargé s’affiche dans le partage `clouddrive` par défaut.

```azurecli-interactive
ls clouddrive
```
