---
author: baanders
description: inclure un fichier décrivant la façon de vérifier qu’une fonction Azure a été publiée
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 543c905cc9e35d985c5b1962067690a0f895eab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531507"
---
### <a name="verify-the-publication-of-your-function"></a>Vérifier la publication de votre fonction

Une fois le processus de publication de la fonction terminé, vous pouvez utiliser ces étapes pour vérifier que la publication a réussi.
 
1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à l’aide de vos informations d’identification.
2. Dans la zone de recherche en haut de la fenêtre, recherchez le nom de votre application de fonction, puis sélectionnez-la.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/search-function-app.png" alt-text="Capture d’écran montrant le portail Azure. Dans le champ de recherche, entrez le nom de l’application de fonction." lightbox="../articles/digital-twins/media/includes/azure-functions/search-function-app.png":::

3. Sur la page **Application de fonction** qui s’ouvre, choisissez **Fonctions** dans le menu de gauche. Recherchez le nom de votre fonction dans la liste pour vérifier qu’elle a été correctement publiée.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png" alt-text="Capture d’écran montrant les fonctions publiées dans le portail Azure" lightbox="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png":::

    > [!Note] 
    > Vous devrez peut-être attendre quelques minutes ou actualiser la page avant que votre fonction ne s’affiche dans la liste des fonctions publiées.