---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: bb1a40b4f8b52181b34d89d40ae58505356e93bf
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891033"
---
Si vous souhaitez récupérer l’URL SAS pour vos données d’entraînement de modèle personnalisé, accédez à votre ressource de stockage dans le portail Azure, puis sélectionnez l’onglet **Explorateur Stockage**. Accédez à votre conteneur, cliquez avec le bouton droit, puis sélectionnez **Obtenir une signature d’accès partagé**. Il est important d’obtenir la signature d’accès partagé de votre conteneur, et non celle du compte de stockage. Vérifiez que les autorisations de **lecture**, d’**écriture**, de **suppression** et de **liste** sont cochées, puis cliquez sur **Créer**. Copiez ensuite la valeur de la section **URL** dans un emplacement temporaire. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
