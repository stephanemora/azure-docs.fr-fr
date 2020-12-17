---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505322"
---
Si vous souhaitez récupérer l’URL SAS pour vos données d’entraînement de modèle personnalisé, accédez à votre ressource de stockage dans le portail Azure, puis sélectionnez l’onglet **Explorateur Stockage**. Accédez à votre conteneur, cliquez avec le bouton droit, puis sélectionnez **Obtenir une signature d’accès partagé**. Il est important d’obtenir la signature d’accès partagé de votre conteneur, et non celle du compte de stockage. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez ensuite la valeur de la section **URL** dans un emplacement temporaire. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![alt-text](../media/quickstarts/get-sas-url.png)