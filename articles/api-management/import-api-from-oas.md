---
title: Importer une spécification OpenAPI à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment importer une spécification OpenAPI avec Gestion des API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: b96cfe9813eef9caf1f1f21e43470a23c7032cb1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072123"
---
# <a name="import-an-openapi-specification"></a>Importer une spécification OpenAPI

Cet article montre comment importer une API de serveur principal de « spécification OpenAPI » se trouvant à l’adresse https://conferenceapi.azurewebsites.net?format=json. Cette API de serveur principal est fournie par Microsoft et hébergée sur Azure. L’article explique également comment tester l’API APIM.

> [!IMPORTANT]
> Consultez ce [document](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) pour obtenir des informations importantes et des conseils liés à l’importation OpenAPI.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une API de serveur principal de spécification OpenAPI
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

Effectuez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API de serveur principal

1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **Spécification OpenAPI** dans la liste **Ajouter une nouvelle API**.
    ![Spécification OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Entrez les paramètres appropriés. Vous pouvez définir toutes les valeurs de l’API lors de la création. Ou bien, vous pouvez définir certaines d’entre elles ultérieurement en accédant à l’onglet **Paramètres**. <br/> Si vous appuyez sur la **touche de tabulation** certains (ou l’ensemble) des champs sont remplis automatiquement avec les informations du service de serveur principal spécifié.

    ![Création d'une API](./media/api-management-get-started/create-api.png)

    |Paramètre|Valeur|Description|
    |---|---|---|
    |**Spécification OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Indique le service implémentant l’API. La gestion des API transmet les demandes à cette adresse.|
    |**Nom complet**|*API de conférence de démonstration*|Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json. <br/>Ce nom s’affiche dans le portail des développeurs.|
    |**Nom**|*demo-conference-api*|Fournit un nom unique pour l’API. <br/>Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json.|
    |**Description**|Fournit une description facultative de l’API.|Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json.|
    |**Suffixe de l’URL de l’API**|*conference*|Le suffixe est ajouté à l’URL de base du service Gestion des API. Gestion des API distingue les API selon leur suffixe. Celui-ci doit donc être unique pour chaque API d'un éditeur donné.|
    |**Modèle d’URL**|*HTTPS*|Détermine les protocoles qui peuvent être utilisés pour accéder à l’API. |
    |**Produits**|*Illimité*| Publiez l’API en l’associant à un produit. Pour éventuellement ajouter cette nouvelle API à un produit, tapez le nom du produit. Cette étape peut être répétée plusieurs fois pour ajouter l'API à plusieurs produits.<br/>Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.<br/> Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits : **Starter** et **Unlimited**. |

4. Sélectionnez **Create** (Créer).

> [!NOTE]
> Les limitations relatives à l’importation d’API sont documentées dans [un autre article](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Tester la nouvelle API APIM dans le portail Azure

Les opérations peuvent être directement appelées depuis le portail Azure, qui permet d’afficher et de tester les opérations d’une API.

![Tester l’API](./media/api-management-get-started/01-import-first-api-01.png)

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.
3. Cliquez sur **GetSpeakers**.

    La page affiche les champs des paramètres de requête, mais ici, nous n’en avons pas. La page affiche également des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance APIM, la clé est renseignée automatiquement, car vous êtes déjà administrateur.
4. Appuyez sur **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

## <a name="call-operation"> </a>Appeler une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations depuis le **portail des développeurs** pour tester les API.

1. Sélectionnez l’API que vous avez créée à l’étape « Importer et publier une API de serveur principal ».
2. Appuyez sur **Portail des développeurs**.

    ![Test dans le portail des développeurs](./media/api-management-get-started/developer-portal.png)

    Le site « Portail des développeurs » s’ouvre.
3. Sélectionnez **l’API**.
4. Sélectionnez **API de conférence de démonstration**.
5. Cliquez sur **GetSpeakers**.

    La page affiche les champs des paramètres de requête, mais ici, nous n’en avons pas. La page affiche également des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance APIM, la clé est renseignée automatiquement, car vous êtes déjà administrateur.
6. Appuyez sur **Essayer**.
7. Appuyez sur **Envoyer**.

    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
