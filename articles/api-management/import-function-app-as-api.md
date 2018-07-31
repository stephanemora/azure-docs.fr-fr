---
title: Importer une application de fonctions Azure Functions en tant qu’API dans le portail Azure | Microsoft Docs
description: Ce didacticiel vous montre comment utiliser le service Gestion des API d’Azure pour importer une application Azure Functions en tant qu’API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239050"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Importer une application Azure Functions en tant qu’API

Cet article explique comment importer une application Azure Functions en tant qu’API. Il explique également comment tester l’API Gestion des API d’Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une application de fonctions Azure Functions en tant qu’API
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

+ Suivez le guide de démarrage rapide [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Vérifiez que votre abonnement contient une application Azure Functions. Pour plus d’informations, consultez [Créer une application de fonctions](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Créez la définition OpenAPI](../azure-functions/functions-openapi-definition.md) de votre application Azure Functions.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API de serveur principal

1. Sous **GESTION DES API**, sélectionnez **API**.
2. Dans la liste **Ajouter une nouvelle API**, sélectionnez **Application de fonctions**.

    ![Application de fonctions](./media/import-function-app-as-api/function-app-api.png)
3. Appuyez sur **Parcourir** pour afficher la liste des applications de fonctions Azure Functions dans votre abonnement.
4. Sélectionnez l’application. Gestion des API recherche le swagger associé à l’application sélectionnée, l’extrait et l’importe. 
5. Ajoutez un suffixe d’URL d’API. Le suffixe est un nom qui identifie cette API spécifique dans cette instance Gestion des API. Le suffixe doit être unique dans cette instance Gestion des API.
6. Publiez l’API en l’associant à un produit. Dans ce cas, le produit **Illimité** est utilisé. Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez ajouter l’API à un produit lorsque vous créez l’API ou ultérieurement.

    Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure plusieurs API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance Gestion des API, vous êtes l’administrateur. Par défaut, les administrateurs sont abonnés à tous les produits.

    Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

    * **Starter**
    * **Illimité**   
7. Sélectionnez **Créer**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Renseigner les clés d’Azure Functions dans Gestion des API Azure

Si les applications Azure Functions importées sont protégées par des clés, le service Gestion des API crée automatiquement des *valeurs nommées* pour celles-ci. En revanche, le service Gestion des API ne renseigne pas les entrées avec des secrets. Effectuez ensuite les étapes suivantes pour chaque entrée :  

1. Accédez à l’onglet **Valeurs nommées** de l’instance Gestion des API.
2. Sélectionnez une entrée, puis l’option **Afficher la valeur** dans la barre latérale.

    ![Valeurs nommées](./media/import-function-app-as-api/apim-named-values.png)

3. Si le texte affiché dans la case **valeur** est similaire au **code pour \<le nom d’Azure Functions\>**, accédez à **Functions Apps**, puis à  **Functions (Fonctions)**.
4. Sélectionnez **Gérer**, puis copiez la clé appropriée en fonction de la méthode d’authentification de votre application.

    ![Application de fonctions - Copier des clés](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Collez la clé dans la case **Valeur**, puis sélectionnez **Enregistrer**.

    ![Application de fonctions - Coller des valeurs de clé](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Tester la nouvelle API Gestion des API dans le portail Azure

Vous pouvez appeler des opérations directement depuis le portail Azure. Le portail Azure est pratique pour afficher et tester les opérations d’une API.  

1. Sélectionnez l’API que vous avez créée dans la section précédente.
2. Sélectionnez l’onglet **Test**.
3. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est **Ocp-Apim-Subscription-Key**, pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance Gestion des API, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 
4. Sélectionnez **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

## <a name="call-operation"> </a>Appeler une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations à partir du portail des développeurs pour tester les API. 

1. Sélectionnez l’API que vous avez créée à l’étape [Importer et publier une API de serveur principal](#create-api).
2. Sélectionnez **Portail des développeurs**.

    Le site « Portail des développeurs » s’ouvre.
3. Sélectionnez l’**API** que vous avez créée.
4. Cliquez sur l’opération que vous souhaitez tester.
5. Sélectionnez **Essayer**.
6. Sélectionnez **Envoyer**.
    
    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)