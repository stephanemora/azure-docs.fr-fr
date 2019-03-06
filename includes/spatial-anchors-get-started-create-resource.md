---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752613"
---
## <a name="create-a-spatial-anchors-resource"></a>Créer une ressource Spatial Anchors

1. Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

2. À partir du menu de gauche du portail Azure, sélectionnez **Créer une ressource**.

3. Recherchez « Spatial Anchors » dans la barre de recherche.

   ![Recherche de Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Sélectionnez **Spatial Anchors (préversion)** pour ouvrir une boîte de dialogue et sélectionner **Créer**.

5. Dans le formulaire **Compte Spatial Anchors** :

   1. Spécifiez un nom de ressource unique.
   2. Sélectionnez l’abonnement auquel attacher la ressource.
   3. Créez un groupe de ressources en sélectionnant **Créer**, nommez le groupe de ressources **myResourceGroup**, puis sélectionnez **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Sélectionnez un emplacement (région) où placer la ressource.
   5. Sélectionnez **Nouveau** pour commencer à créer la ressource.

   ![Créer une ressource](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Une fois la ressource correctement créée, les propriétés de cette ressource sont consultables. Prenez note de l’**ID de compte** de la ressource, car vous allez en avoir besoin ultérieurement.

   ![Afficher les propriétés de la ressource](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Sous **Paramètres**, sélectionnez **Clé** et prenez note de la valeur de **Clé primaire**. Cette valeur représente `Account Key` qui sera utile plus tard.

   ![Afficher la clé du compte](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
