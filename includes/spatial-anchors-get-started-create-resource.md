---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305317"
---
## <a name="create-a-spatial-anchors-resource"></a>Créer une ressource Spatial Anchors

Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

Dans le volet de navigation gauche du portail Azure, sélectionnez **Créer une ressource**.

Utilisez la zone de recherche pour rechercher **Spatial Anchors**.

   ![Recherche de Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Sélectionnez **Spatial Anchors**. Dans la boîte de dialogue, sélectionnez **Créer**.

Dans la boîte de dialogue **Compte Spatial Anchors** :

- Entrez un nom de ressource unique.
- Sélectionnez l’abonnement auquel attacher la ressource.
- Créer un groupe de ressources en sélectionnant **Créer**. Nommez-le **myResourceGroup** et sélectionnez **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Sélectionnez un emplacement (région) où placer la ressource.
- Sélectionnez **Nouveau** pour commencer à créer la ressource.

   ![Créer une ressource](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Une fois la ressource créée, vous pouvez voir les propriétés de ressource. Copiez l’**ID de compte** de la ressource dans un éditeur de texte, car vous en aurez besoin par la suite.

   ![Propriétés de la ressource](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Sous **Paramètres**, sélectionnez **Clé**. Copiez la valeur de la **Clé primaire** dans un éditeur de texte. Cette valeur est la `Account Key`. Vous en aurez besoin ultérieurement.

   ![Clé de compte](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
