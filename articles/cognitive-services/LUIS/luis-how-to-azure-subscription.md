---
title: Guide pratique pour créer et gérer des ressources LUIS
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser et gérer des ressources Azure pour une application LUIS.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 60e5d37f57d6a87d9190650f044a1ffc991e60fb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110096016"
---
# <a name="how-to-create-and-manage-luis-resources"></a>Guide pratique pour créer et gérer des ressources LUIS

Utilisez cet article pour découvrir les types de ressources Azure que vous pouvez utiliser avec LUIS, et comment les gérer.

## <a name="authoring-resource"></a>Ressource de création

Une ressource de création vous permet de créer, de gérer, d’entraîner, de tester et de publier vos applications. Un [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) est disponible pour la ressource de création LUIS, le niveau gratuit (F0), qui vous donne les éléments suivants :

* 1 million de transactions de création 
* 1 000 requêtes de point de terminaison de prédiction de test par mois.

Vous pouvez utiliser les [API programmatiques LUIS v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) pour gérer les ressources de création. 

## <a name="prediction-resource"></a>Ressource de prédiction

Une ressource de prédiction vous permet d’interroger votre point de terminaison de prédiction au-delà des 1 000 requêtes fournies par la ressource de création. Deux [niveaux tarifaires](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) sont disponibles pour la ressource de prédiction :

* La ressource de prédiction gratuite (F0), qui vous donne 10 000 requêtes de point de terminaison de prédiction par mois.
* La ressource de prédiction standard (S0), qui correspond au niveau payant. 

Vous pouvez utiliser l’[API de point de terminaison LUIS v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5f68f4d40a511ce5a7440859) pour gérer les ressources de prédiction.

> [!Note]
> * Vous pouvez également utiliser une [ressource multiservice](../cognitive-services-apis-create-account-cli.md?tabs=multiservice) pour obtenir un seul point de terminaison qui peut servir pour plusieurs services Cognitive Services.
> * LUIS fournit deux types de ressource F0 (niveau gratuit) : l’une pour les transactions de création et l’autre pour les transactions de prédiction. Si vous épuisez le quota gratuit de transactions de prédiction, veillez à utiliser la ressource de prédiction F0, qui vous offre 10 000 transactions gratuites par mois, et non la ressource de création, qui vous offre seulement 1 000 transactions de prédiction par mois.
> * Vous devez créer des applications LUIS dans les [régions](luis-reference-regions.md#publishing-regions) où vous souhaitez effectuer des opérations de publication et d’interrogation.

## <a name="create-luis-resources"></a>Créer des ressources LUIS

Pour créer des ressources LUIS, vous pouvez utiliser le portail LUIS, le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) ou Azure CLI. Une fois que vous avez créé vos ressources, vous devez les affecter à vos applications pour qu’elles les utilisent.

# <a name="luis-portal"></a>[Portail LUIS](#tab/portal)

### <a name="create-a-luis-authoring-resource-using-the-luis-portal"></a>Créer une ressource de création LUIS à l’aide du portail LUIS

1. Connectez-vous au [portail LUIS](https://www.luis.ai), sélectionnez votre pays/région, puis acceptez les conditions d’utilisation. Si vous voyez la section **Mes applications** dans le portail, il existe déjà une ressource LUIS et vous pouvez ignorer l’étape suivante.

2. Dans la fenêtre **Choisir une ressource de création** qui s’affiche, recherchez votre abonnement Azure et la ressource de création LUIS. Si vous n’avez pas de ressource, vous pouvez en créer une nouvelle.

    :::image type="content" source="./media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Choisissez un type de ressource de création LUIS.":::
    
    Quand vous créez une ressource de création, fournissez les informations suivantes :
    * **Nom du locataire** : locataire auquel votre abonnement Azure est associé.
    * **Nom de l’abonnement Azure** : abonnement qui sera facturé pour la ressource.
    * **Nom du groupe de ressources Azure** : nom de groupe de ressources personnalisé que vous choisissez ou créez. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.
    * **Nom de la ressource Azure** : nom personnalisé que vous choisissez, utilisé comme partie de l’URL pour vos requêtes de point de terminaison de création et de prédiction.
    * **Niveau tarifaire** : le niveau tarifaire détermine le nombre maximal de transactions par seconde et par mois.

### <a name="create-a-luis-prediction-resource-using-the-luis-portal"></a>Créer une ressource de prédiction LUIS à l’aide du portail LUIS

[!INCLUDE [Create LUIS Prediction resource in LUIS portal](./includes/add-prediction-resource-portal.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

### <a name="create-luis-resources-in-the-azure-cli"></a>Créer des ressources LUIS dans l’interface Azure CLI

Utilisez [Azure CLI](/cli/azure/install-azure-cli) pour créer chaque ressource individuellement.

> [!TIP]
> * La valeur `kind` de la ressource de création est `LUIS.Authoring`  
> * La valeur `kind` de la ressource de prédiction est `LUIS` 

1. Connectez-vous à Azure CLI :

    ```azurecli
    az login
    ```

    Cette commande ouvre un navigateur pour vous permettre de sélectionner le compte approprié et de fournir l’authentification nécessaire.

2. Créez une ressource de création LUIS dont le genre est `LUIS.Authoring` et le nom `my-luis-authoring-resource`. Créez-la dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

3. Créez une ressource de point de terminaison de prédiction LUIS dont le genre est `LUIS` et le nom `my-luis-prediction-resource`. Créez-la dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`. Si vous souhaitez avoir un débit plus élevé que celui du niveau Gratuit, passez de `F0` à `S0`. [Découvrez plus en détail les niveaux tarifaires et le débit.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

---


## <a name="assign-luis-resources"></a>Affecter des ressources LUIS

La création d’une ressource ne signifie pas nécessairement qu’elle est mise en œuvre. Vous devez l’affecter à vos applications. Vous pouvez affecter une ressource de création pour une seule application ou pour toutes les applications dans LUIS.

# <a name="luis-portal"></a>[Portail LUIS](#tab/portal)

### <a name="assign-resources-using-the-luis-portal"></a>Affecter des ressources à l’aide du portail LUIS

**Affecter une ressource de création à toutes vos applications** 

 La procédure suivante affecte la ressource de création à toutes les applications.

1. Connectez-vous au [portail LUIS](https://www.luis.ai).
1. Dans le coin supérieur droit, sélectionnez votre compte d’utilisateur, puis **Paramètres**.
1. Dans la page **Paramètres utilisateur**, sélectionnez **Ajouter une ressource de création**, puis sélectionnez une ressource de création existante. Sélectionnez **Enregistrer**.

**Affecter une ressource à l’aide d’une application spécifique**

La procédure suivante affecte une ressource à une application spécifique.

1. Connectez-vous au [portail LUIS](https://www.luis.ai). Sélectionnez une application dans la liste **My apps** (Mes applications).
1. Accédez à **Gérer** > **Ressources Azure** :

    :::image type="content" source="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png" alt-text="Choisissez un type de ressource de prédiction LUIS (Language Understanding)." lightbox="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png":::

1. Sous l’onglet **Ressource de prédiction** ou **Ressource de création**, sélectionnez le bouton **Ajouter une ressource de prédiction** ou **Ajouter une ressource de création**.
1. Utilisez les champs du formulaire pour trouver la ressource appropriée, puis sélectionnez **Enregistrer**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="assign-prediction-resource-programmatically"></a>Affecter une ressource de prédiction programmatiquement 

Pour les processus automatisés comme les pipelines CI/CD, vous pouvez automatiser l’affectation d’une ressource LUIS à une application LUIS en effectuant les étapes suivantes :

1. Obtenez un [jeton Azure Resource Manager](https://resources.azure.com/api/token?plaintext=true) qui est une chaîne alphanumérique de caractères. Ce jeton a un délai d’expiration. Veillez donc à l’utiliser immédiatement. Vous pouvez également utiliser la commande Azure CLI suivante.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
1. Utilisez le jeton pour demander les ressources de runtime LUIS parmi les différents abonnements. Utilisez l’API pour [obtenir le compte LUIS Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) auquel votre compte d’utilisateur a accès.

    Cette API POST nécessite les valeurs suivantes :

    |En-tête|Valeur|
    |--|--|
    |`Authorization`|La valeur de `Authorization` est `Bearer {token}`. La valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |`Ocp-Apim-Subscription-Key`|Votre clé de création.|

    L’API retourne un tableau d’objets JSON qui représentent vos abonnements LUIS. Les valeurs, qui incluent l’ID d’abonnement, le groupe de ressources et le nom de ressource, sont retournées en tant que `AccountName`. Recherchez dans le tableau l’élément correspondant à la ressource LUIS que vous souhaitez affecter à l’application LUIS.

1. Affectez le jeton à la ressource LUIS à l’aide de l’API d’[affectation de compte LUIS Azure à une application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515).

    Cette API POST nécessite les valeurs suivantes :

    |Type|Paramètre|Valeur|
    |--|--|--|
    |En-tête|`Authorization`|La valeur de `Authorization` est `Bearer {token}`. La valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |En-tête|`Ocp-Apim-Subscription-Key`|Votre clé de création.|
    |En-tête|`Content-type`|`application/json`|
    |Querystring|`appid`|L’ID d’application LUIS.
    |body||{`AzureSubscriptionId` : votre ID d’abonnement,<br>`ResourceGroup` : nom du groupe de ressources qui contient votre ressource de prédiction,<br>`AccountName` : nom de votre ressource de prédiction}|

    Quand cette API réussit son exécution, elle retourne `201 - created status`.

---

## <a name="unassign-a-resource"></a>Annuler l’affectation d’une ressource

Quand vous annulez l’affectation d’une ressource, elle n’est pas supprimée d’Azure. Elle est uniquement dissociée de LUIS.

# <a name="luis-portal"></a>[Portail LUIS](#tab/portal)

## <a name="unassign-resources-using-luis-portal"></a>Annuler l’affectation de ressources à l’aide du portail LUIS

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis sélectionnez une application dans la liste **Mes applications**.
1. Accédez à **Gérer** > **Ressources Azure**.
1. Sélectionnez le bouton **Annuler l’affectation de la ressource** pour la ressource.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="unassign-prediction-resource-programmatically"></a>Annuler l’affectation d’une ressource de prédiction par programmation

1. Obtenez un [jeton Azure Resource Manager](https://resources.azure.com/api/token?plaintext=true) qui est une chaîne alphanumérique de caractères. Ce jeton a un délai d’expiration. Veillez donc à l’utiliser immédiatement. Vous pouvez également utiliser la commande Azure CLI suivante.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
 
1. Utilisez le jeton pour demander les ressources de runtime LUIS parmi les différents abonnements. Utilisez l’[API de récupération des comptes LUIS Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), à laquelle votre compte d’utilisateur a accès.

    Cette API POST nécessite les valeurs suivantes :

    |En-tête|Valeur|
    |--|--|
    |`Authorization`|La valeur de `Authorization` est `Bearer {token}`. La valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |`Ocp-Apim-Subscription-Key`|Votre clé de création.|

    L’API retourne un tableau d’objets JSON qui représentent vos abonnements LUIS. Les valeurs, qui incluent l’ID d’abonnement, le groupe de ressources et le nom de ressource, sont retournées en tant que `AccountName`. Recherchez dans le tableau l’élément correspondant à la ressource LUIS que vous souhaitez affecter à l’application LUIS.

1. Affectez le jeton à la ressource LUIS à l’aide de l’API [Annuler l’affectation d’un compte LUIS Azure d’une application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32554f8591db3a86232e1/console).

    Cette API de suppression nécessite les valeurs suivantes :

    |Type|Paramètre|Valeur|
    |--|--|--|
    |En-tête|`Authorization`|La valeur de `Authorization` est `Bearer {token}`. La valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |En-tête|`Ocp-Apim-Subscription-Key`|Votre clé de création.|
    |En-tête|`Content-type`|`application/json`|
    |Querystring|`appid`|L’ID d’application LUIS.
    |body||{`AzureSubscriptionId` : votre ID d’abonnement,<br>`ResourceGroup` : nom du groupe de ressources qui contient votre ressource de prédiction,<br>`AccountName` : nom de votre ressource de prédiction}|

    Quand cette API réussit son exécution, elle retourne `200 - OK status`.

---

## <a name="resource-ownership"></a>Propriété des ressources

Une ressource Azure, par exemple une ressource LUIS, appartient à l’abonnement qui contient la ressource.

Pour changer la propriété d’une ressource, vous pouvez effectuer l’une des actions suivantes :
* Transférer la [propriété](../../cost-management-billing/manage/billing-subscription-transfer.md) de votre abonnement.
* Exporter l’application LUIS en tant que fichier, puis importer l’application dans un autre abonnement. L’exportation est disponible dans la page **Mes applications** du portail LUIS.

## <a name="resource-limits"></a>Limites des ressources

### <a name="authoring-key-creation-limits"></a>Limites de création de clés de création

Vous pouvez générer jusqu’à 10 clés de création par région et par abonnement. Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication dans laquelle vous souhaitez que votre application cliente soit située. Pour plus d’informations sur la façon dont les régions de création correspondent aux régions de publication, consultez [Régions de création et de publication](luis-reference-regions.md). 

Pour plus d’informations sur les limites de clés, consultez [Limites de clés](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Erreurs liées aux limites d’utilisation des clés

Les limites d’utilisation dépendent du niveau tarifaire.

Si vous dépassez votre quota de transactions par seconde (TPS), vous recevez une erreur HTTP 429. Si vous dépassez votre quota de transactions par mois (TPM), vous recevez une erreur HTTP 403.

## <a name="change-the-pricing-tier"></a>Changer le niveau tarifaire

1.  Dans le [portail Azure](https://portal.azure.com), accédez à **Toutes les ressources**, puis sélectionnez votre ressource

    :::image type="content" source="./media/luis-usage-tiers/find.png" alt-text="Capture d’écran montrant un abonnement LUIS dans le portail Azure." lightbox="./media/luis-usage-tiers/find.png":::

1.  Dans le menu de gauche, sélectionnez **Niveau tarifaire** pour voir les niveaux tarifaires disponibles
1.  Sélectionnez le niveau tarifaire souhaité, puis cliquez sur **Sélectionner** pour enregistrer le changement apporté. Une fois le changement de tarif terminé, une notification s’affiche en haut à droite avec la mise à jour du niveau tarifaire.

## <a name="view-azure-resource-metrics"></a>Voir les métriques de ressources Azure

## <a name="view-a-summary-of-azure-resource-usage"></a>Voir un récapitulatif de l’utilisation des ressources Azure
Vous pouvez voir les informations relatives à l’utilisation de LUIS dans le portail Azure. La page **Vue d’ensemble** affiche un récapitulatif, notamment des informations récentes sur les appels et les erreurs. Si vous effectuez une demande de point de terminaison LUIS, attendez au moins cinq minutes, le temps que le changement soit visible.

:::image type="content" source="./media/luis-usage-tiers/overview.png" alt-text="Capture d’écran montrant la page de vue d’ensemble." lightbox="./media/luis-usage-tiers/overview.png":::

## <a name="customizing-azure-resource-usage-charts"></a>Personnalisation des graphiques d’utilisation des ressources Azure
La page **Métriques** fournit une vue plus détaillée des données. Vous pouvez configurer vos graphiques de métriques pour une **période** et une **métrique** spécifiques.

:::image type="content" source="./media/luis-usage-tiers/metrics.png" alt-text="Capture d’écran montrant la page Métriques." lightbox="./media/luis-usage-tiers/metrics.png":::

## <a name="total-transactions-threshold-alert"></a>Alerte de seuil de nombre total de transactions
Si vous souhaitez savoir à quel moment vous atteignez un certain seuil de transactions (10 000 transactions, par exemple), vous pouvez créer une alerte :

1. Dans le menu de gauche, sélectionnez **Alertes**
2. Dans la barre de menus supérieure, sélectionnez **Nouvelle règle d’alerte**

    :::image type="content" source="./media/luis-usage-tiers/alerts.png" alt-text="Capture d’écran montrant la page des règles d’alerte." lightbox="./media/luis-usage-tiers/alerts.png":::

3. Cliquez sur **Ajouter une condition**

    :::image type="content" source="./media/luis-usage-tiers/alerts-2.png" alt-text="Capture d’écran montrant la page Ajouter une condition pour les règles d’alerte." lightbox="./media/luis-usage-tiers/alerts-2.png":::

4. Sélectionnez **Total des appels**

    :::image type="content" source="./media/luis-usage-tiers/alerts-3.png" alt-text="Capture d’écran montrant la page Total des appels pour les alertes." lightbox="./media/luis-usage-tiers/alerts-3.png":::

5. Faites défiler l’affichage jusqu’à la section **Logique d’alerte**, définissez les attributs comme vous le souhaitez, puis cliquez sur **Terminé**

    :::image type="content" source="./media/luis-usage-tiers/alerts-4.png" alt-text="Capture d’écran montrant la page Logique d’alerte." lightbox="./media/luis-usage-tiers/alerts-4.png":::

6. Pour envoyer des notifications ou appeler des actions quand la règle d’alerte se déclenche, accédez à la section **Actions**, puis ajoutez votre groupe d’actions.

    :::image type="content" source="./media/luis-usage-tiers/alerts-5.png" alt-text="Capture d’écran montrant la page Actions pour les alertes." lightbox="./media/luis-usage-tiers/alerts-5.png":::

### <a name="reset-an-authoring-key"></a>Réinitialiser une clé de création

Pour les applications de [ressource de création migrées](luis-migration-authoring.md) : Si votre clé de création est compromise, réinitialisez-la dans le portail Azure, dans la page **Clés** de la ressource de création.

Pour les applications qui n’ont pas été migrées : La clé est réinitialisée pour toutes vos applications dans le portail LUIS. Si vous éditez vos applications par le biais des API de création, vous devez changer la valeur de `Ocp-Apim-Subscription-Key` pour la nouvelle clé.

### <a name="regenerate-an-azure-key"></a>Regénérer une clé Azure

Vous pouvez regénérer une clé Azure à partir de la page **Clés** du portail Azure.

<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Propriété, accès et sécurité des applications

Une application est définie par ses ressources Azure, lesquelles sont déterminées par l’abonnement du propriétaire.

Vous pouvez déplacer votre application LUIS. Pour ce faire, utilisez les ressources suivantes via le portail Azure ou Azure CLI :

* [Déplacer une application entre des ressources de création LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Déplacer une ressource vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Déplacer une ressource au sein du même abonnement ou entre des abonnements distincts](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment utiliser des versions](luis-how-to-manage-versions.md) pour contrôler le cycle de vie de votre application.


