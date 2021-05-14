---
title: Importer une application logique en tant qu’API avec le portail Azure | Microsoft Docs
description: Cet article vous montre comment utiliser le service Gestion des API (APIM) pour importer une application logique en tant qu’API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 6825e5d7849f97aac4627d4856d26b3e08ab6761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2021
ms.locfileid: "108230570"
---
# <a name="import-a-logic-app-as-an-api"></a>Importer une application logique en tant qu’API

Cet article explique comment importer une application logique en tant qu’API, et tester l’API importée.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> -   Importer une application logique en tant qu’API
> -   Tester l’API dans le portail Azure

## <a name="prerequisites"></a>Prérequis

-   Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
-   Assurez-vous que votre abonnement contient une application logique qui expose un point de terminaison HTTP. Pour plus d’informations, consultez l’article [Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques](../logic-apps/logic-apps-http-endpoint.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importer et publier une API back-end

1. À partir du portail Azure, accédez à votre service Gestion des API, puis sélectionnez **API** dans le menu.
1. Sélectionnez **Application logique** dans la liste **Ajouter une nouvelle API**.

    :::image type="content" source="./media/import-logic-app-as-api/logic-app-select.png" alt-text="Sélectionner une catégorie d’application logique":::

1. Appuyez sur **Parcourir** pour afficher la liste des applications logiques avec déclencheur HTTP de votre abonnement. 
    * Les applications logiques *sans* déclencheur HTTP n’apparaissent pas dans la liste.

    :::image type="content" source="./media/import-logic-app-as-api/browse-logic-apps.png" alt-text="Rechercher les applications logiques existantes avec un déclencheur correct":::

1. Sélectionnez l’application logique. 

    :::image type="content" source="./media/import-logic-app-as-api/select-logic-app-import-2.png" alt-text="Sélectionner l’application logique":::

1. Gestion des API recherche le swagger associé à l’application sélectionnée, l’extrait et l’importe.
1. Ajoutez un suffixe d’URL d’API. 
    * Le suffixe identifie cette API spécifique de façon unique dans cette instance Gestion des API.

    :::image type="content" source="./media/import-logic-app-as-api/create-from-logic-app.png" alt-text="Compléter les champs":::

1. Si vous voulez que l’API soit publiée et disponible pour les développeurs, basculez vers la vue **complète** et associez-la à un **produit**. Nous utilisons le produit *« illimité »* dans cet exemple. 
    * Vous pouvez ajouter votre API à un produit au moment de la création ou plus tard par le biais de l’onglet **Paramètres**.

    >[!NOTE]
    > Les produits sont des associations d’une ou de plusieurs API proposées aux développeurs par le biais du portail des développeurs. Les développeurs doivent d’abord s’abonner à un produit pour obtenir l’accès à l’API. Une fois abonnés, ils obtiennent une clé d’abonnement pour toutes les API de ce produit. En tant que créateur de l’instance Gestion des API, vous êtes administrateur et abonné à chaque produit par défaut.
    >
    > Chaque instance Gestion des API est fournie avec deux exemples de produits par défaut :
    > - **Starter**
    > - **Illimité**

1. Entrez d’autres paramètres d’API. 
    * Vous pouvez définir ces valeurs au moment de la création ou plus tard en accédant à l’onglet **Paramètres**. Les paramètres sont expliqués dans le tutoriel [Importer et publier votre première API](import-and-publish.md#import-and-publish-a-backend-api).
1. Sélectionnez **Create** (Créer).

## <a name="test-the-api-in-the-azure-portal"></a>Tester l’API dans le portail Azure

Les opérations peuvent être directement appelées depuis le portail Azure, qui permet d’afficher et de tester les opérations d’une API.

:::image type="content" source="./media/import-logic-app-as-api/test-logic-app-api.png" alt-text="Tester l’application logique":::

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.
3. Cliquez sur l’opération que vous souhaitez tester.

    * La page affiche des champs pour les paramètres de requête et les en-têtes. 
    * L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement au produit associée à cette API. 
    * En tant que créateur de l’instance Gestion des API, vous êtes déjà administrateur. Par conséquent, la clé est renseignée automatiquement.

4. Appuyez sur **Envoyer**.

    * Quand le test réussit, le back-end répond par **200 OK** et des données.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Chaque application logique a une opération **manual-invoke**. Pour inclure votre API de plusieurs applications logiques et éviter les collisions, vous devez renommer la fonction.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>
> [Transformer et protéger une API publiée](transform-api.md)
