---
title: Importer une application de fonction Azure en tant qu’API dans Gestion des API
titleSuffix: Azure API Management
description: Cet article vous montre comment importer une application Azure Function en tant qu’API dans le service Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: cf4ff32af5b0960d22496512e8d624df0f4cf5e5
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669243"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importer une application de fonction Azure en tant qu’API dans Gestion des API Azure

Le service Gestion des API Azure prend en charge l’importation des applications Azure Function en tant que nouvelles API ou en les ajoutant à des API existantes. Le processus génère automatiquement une clé d’hôte dans l’application de fonction Azure, qui est ensuite assignée à une valeur nommée dans le service Gestion des API Azure.

Cet article vous guide tout au long de la procédure d’importation et de test d’une application de fonction Azure en tant qu’API dans le service Gestion des API Azure. 

Vous apprendrez à :

> [!div class="checklist"]
> * Importer une application de fonction Azure en tant qu’API
> * Ajouter une application de fonction Azure à une API
> * Afficher la clé d’hôte de la nouvelle application de fonction Azure et la valeur nommée du service Gestion des API Azure
> * Tester l’API dans le portail Azure

## <a name="prerequisites"></a>Prérequis

* Suivez le guide de démarrage rapide [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
* Vérifiez que votre abonnement contient une application Azure Functions. Pour plus d’informations, consultez [Création d’une application Azure Function](../azure-functions/functions-get-started.md). Les fonctions doivent avoir un déclencheur HTTP et un niveau d’autorisation défini sur *Anonyme* ou *Fonction*.

> [!NOTE]
> Vous pouvez importer et gérer vos API à l’aide de l’extension Gestion des API pour Visual Studio Code. Suivez le [tutoriel sur l’extension Gestion des API](visual-studio-code-tutorial.md) pour effectuer l’installation et commencer.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importer une application de fonction Azure en tant que nouvelle API

Suivez les étapes ci-dessous pour créer une nouvelle API à partir d’une application de fonction Azure.

1. À partir du portail Azure, accédez à votre service Gestion des API, puis sélectionnez **API** dans le menu.

2. Dans la liste **Ajouter une nouvelle API**, sélectionnez **Application de fonction**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Capture d’écran montrant la vignette Application de fonction.":::

3. Cliquez sur **Parcourir** pour sélectionner des fonctions à importer.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Capture d’écran mettant en évidence le bouton Parcourir.":::

4. Cliquez sur la section **Application de fonction** pour afficher la liste des applications de fonction disponibles.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Capture d’écran mettant en évidence la section Application de fonction.":::

5. Recherchez l’application de fonction à partir de laquelle vous souhaitez importer des fonctions, cliquez dessus, puis appuyez sur **Sélectionner**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Capture d’écran mettant en évidence l’application de fonction à partir de laquelle vous souhaitez importer des fonctions et le bouton Sélectionner.":::

6. Sélectionnez les fonctions que vous souhaitez importer et cliquez sur **Sélectionner**.
    * Vous pouvez uniquement importer des fonctions sur la base du déclencheur HTTP avec les niveaux d’autorisation *Anonyme* ou *Fonction*.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Capture d’écran mettant en évidence les fonctions à importer et le bouton Sélectionner.":::

7. Passez à la vue **Complète** et affectez **Produit** à votre nouvelle API. 
8. Si nécessaire, spécifiez d’autres champs lors de la création, ou configurez-les ultérieurement par le biais de l’onglet **Paramètres**. 
    * Les paramètres sont expliqués dans le tutoriel [Importer et publier votre première API](import-and-publish.md#import-and-publish-a-backend-api).

    >[!NOTE]
    > Les produits sont des associations d’une ou de plusieurs API proposées aux développeurs par le biais du portail des développeurs. Les développeurs doivent d’abord s’abonner à un produit pour obtenir l’accès à l’API. Une fois abonnés, ils obtiennent une clé d’abonnement pour toutes les API de ce produit. En tant que créateur de l’instance Gestion des API, vous êtes administrateur et abonné à chaque produit par défaut.
    >
    > Chaque instance Gestion des API est fournie avec deux exemples de produits par défaut :
    > - **Starter**
    > - **Illimité**

9. Cliquez sur **Créer**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Ajouter une application de fonction Azure à une API existante

Suivez les étapes ci-dessous pour ajouter une application de fonction Azure à une API existante.

1. Dans votre instance du service **Gestion des API Azure**, sélectionnez **API** dans le menu de gauche.

2. Choisissez l’API dans laquelle vous souhaitez importer une application de fonction Azure. Cliquez sur **...** et sélectionnez **Importer** dans le menu contextuel.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Capture d’écran mettant en évidence l’option de menu Importer.":::

3. Cliquez sur la vignette **Application de fonction**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Capture d’écran mettant en évidence la vignette Application de fonction.":::

4. Dans la fenêtre indépendante qui s’affiche, cliquez sur **Parcourir**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Capture d’écran montrant le bouton Parcourir.":::

5. Cliquez sur la section **Application de fonction** pour afficher la liste des applications de fonction disponibles.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Capture d’écran mettant en évidence la liste d’applications de fonction.":::

6. Recherchez l’application de fonction à partir de laquelle vous souhaitez importer des fonctions, cliquez dessus, puis appuyez sur **Sélectionner**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Capture d’écran mettant en évidence l’application de fonction à partir de laquelle vous souhaitez importer des fonctions.":::

7. Sélectionnez les fonctions que vous souhaitez importer et cliquez sur **Sélectionner**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Capture d’écran mettant en évidence les fonctions que vous souhaitez importer.":::

8. Cliquez sur **Importer**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Ajouter à partir d’une application de fonction":::

## <a name="authorization"></a><a name="authorization"></a> Autorisation

L’importation d’une application de fonction Azure génère automatiquement :

* Une clé d’hôte au sein de l’application de fonction portant le nom apim-{*nom de votre instance de service Gestion des API Azure*}.
* Une valeur nommée au sein de l’instance de Gestion des API Azure portant le nom {*nom de votre instance d’Azure Function App*}-key, qui contient la clé d’hôte créée.

Pour les API créées après le 4 avril 2019, la clé d’hôte est passée dans les requêtes HTTP de la Gestion des API vers l’application de fonction dans un en-tête. Les API plus anciennes transmettent la clé d’hôte sous la forme d’[un paramètre de requête](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Vous pouvez changer ce comportement par le biais de l’[appel à l’API REST](/rest/api/apimanagement/2020-12-01/backend/update#backendcredentialscontract) `PATCH Backend` sur l’entité *Back-end* associée à l’application de fonction.

> [!WARNING]
> En cas de suppression ou de modification de la valeur de la clé d’hôte Azure Function App ou de la valeur nommée de Gestion des API Azure, la communication entre les services est interrompue. Les valeurs ne se synchronisent pas automatiquement.
>
> Si vous avez besoin de modifier la clé d’hôte, assurez-vous que la valeur nommée est également modifiée dans le service Gestion des API Azure.

### <a name="access-azure-function-app-host-key"></a>Accéder à la clé d’hôte d’application de fonction Azure

1. Accédez à votre instance d’application de fonction Azure.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Capture d’écran qui met en évidence la sélection de votre instance d’application de fonction.":::

2. Dans la section **Fonctions** du menu de navigation latéral, sélectionnez **Clés d’application**.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Capture d’écran mettant en évidence l’option des paramètres d’applications de fonction.":::

3. Recherchez les clés sous la section **Clés d’hôte**.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Capture d’écran mettant en évidence la section Clés d’accès.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Accéder à la valeur nommée dans le service Gestion des API Azure

Accédez à votre instance de Gestion des API Azure et sélectionnez **Valeurs nommées** dans le menu de gauche. Vous y trouverez la clé Azure Function App.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Ajouter à partir d’une application de fonction":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Tester la nouvelle API dans le Portail Azure

Vous pouvez appeler des opérations directement depuis le portail Azure. Le portail Azure est pratique pour afficher et tester les opérations d’une API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Capture d’écran qui met en évidence la procédure de test.":::

1. Sélectionnez l’API que vous avez créée dans la section précédente.

2. Sélectionnez l’onglet **Test**.

3. Cliquez sur l’opération que vous souhaitez tester.

    * La page affiche des champs pour les paramètres de requête et les en-têtes. 
    * L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement au produit associée à cette API. 
    * En tant que créateur de l’instance Gestion des API, vous êtes déjà administrateur. Par conséquent, la clé est renseignée automatiquement. 

4. Sélectionnez **Envoyer**.

    * Quand le test réussit, le back-end répond avec **200 OK** et des données.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
