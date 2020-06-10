---
title: Comment utiliser les clés de création et de runtime – LUIS
description: Lorsque vous utilisez Language Understanding (LUIS) pour la première fois, vous n’avez pas besoin de créer de clé de création. Lorsque vous avez l’intention de publier l’application, puis d’utiliser votre point de terminaison de runtime, vous devez créer et attribuer la clé de runtime à l’application.
services: cognitive-services
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: c566e8fe56d19856f5a577e472929b7610497d7c
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344456"
---
# <a name="create-luis-resources"></a>Créer des ressources LUIS

Les ressources de création et de runtime fournissent l’authentification à votre application LUIS et au point de terminaison de prédiction.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Quand vous vous connectez au portail LUIS, vous pouvez choisir de continuer avec :

* une [clé d’essai](#trial-key) gratuit qui fournit des requêtes de création et de point de terminaison de prédiction ;
* une ressource de [création LUIS](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) Azure.

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Se connecter au portail LUIS et commencer à créer

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et acceptez les conditions d’utilisation.
1. Commencez votre application LUIS en choisissant le type de clé de création LUIS que vous souhaitez utiliser : clé d’essai gratuit ou nouvelle clé de création LUIS Azure.

    ![Choisir un type de ressource de création Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Une fois le processus de sélection des ressources terminé, [créez une application](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Clé d’essai

La clé d’essai (démarrage) vous est fournie. Elle est utilisée comme clé d’authentification pour interroger le runtime de point de terminaison de prédiction, avec jusqu’à 1 000 requêtes par mois.

Elle est visible à la fois dans la page **Paramètres utilisateur** et dans les pages **Gérer -> Ressources Azure** du portail .

Lorsque vous êtes prêt à publier votre point de terminaison de prédiction, [créez](#create-luis-resources) et [attribuez](#assign-a-resource-to-an-app) les clés de création et de runtime de prédiction pour remplacer la fonctionnalité de clé de démarrage.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Créer des ressources dans Azure CLI

Utilisez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour créer chaque ressource individuellement.

Ressource `kind` :

* Création : `LUIS.Authoring`
* Prédiction : `LUIS`

1. Connectez-vous à Azure CLI :

    ```azurecli
    az login
    ```

    Cette action ouvre un navigateur qui vous permet de sélectionner le bon compte et de fournir une authentification.

1. Créez une **ressource de création LUIS**, de type `LUIS.Authoring`, nommée `my-luis-authoring-resource` dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Créez une **ressource de point de terminaison de prédiction LUIS**, de type `LUIS`, nommée `my-luis-prediction-resource` dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`. Si vous recherchez un débit plus élevé que le niveau gratuit, passez de `F0` à `S0`. En savoir plus sur les [niveaux tarifaires et le débit](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Ces clés ne sont **pas** utilisées par le portail LUIS tant qu’elles n’ont pas été attribuées dans le portail LUIS via le menu **Gérer -> Ressources Azure**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Affecter une ressource de création dans le portail LUIS pour toutes les applications

Vous pouvez affecter une ressource de création pour une seule application ou pour toutes les applications dans LUIS. La procédure suivante affecte toutes les applications à une seule ressource de création.

1. Connectez-vous au [portail LUIS](https://www.luis.ai).
1. Dans la barre de navigation supérieure, tout à fait à droite, sélectionnez votre compte d’utilisateur, puis sélectionnez **Paramètres**.
1. Dans la page **Paramètres utilisateur**, sélectionnez **Ajouter une ressource de création**, puis sélectionnez une ressource de création existante. Sélectionnez **Enregistrer**.

## <a name="assign-a-resource-to-an-app"></a>Affecter une ressource à une application

Vous pouvez affecter à une application une ressource unique, runtime de création ou de point de terminaison de prédiction, en procédant comme suit.

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis sélectionnez une application dans la liste **Mes applications**.
1. Accédez à la page **Gérer -> Ressources Azure**.

    ![Sélectionnez Gérer -> Ressources Azure dans le portail LUIS pour affecter une ressource à l’application.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Sélectionnez l’onglet de ressource Prédiction ou Création, puis le bouton **Ajouter une ressource de prédiction** ou **Ajouter une ressource de création**.
1. Sélectionnez les champs dans le formulaire pour trouver la ressource correcte, puis sélectionnez **Enregistrer**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Affecter une ressource de runtime sans utiliser le portail LUIS

À des fins d’automation (par exemple pour le pipeline CI/CD), vous pouvez automatiser l’affectation d’une ressource de runtime LUIS à une application LUIS. Pour ce faire, vous devez suivre les étapes ci-dessous :

1. Obtenir un jeton Azure Resource Manager à partir de ce [site web](https://resources.azure.com/api/token?plaintext=true). Ce jeton expire ; veillez donc à l’utiliser immédiatement. La requête retourne un jeton Azure Resource Manager.

    ![Demander un jeton Azure Resource Manager et recevoir un jeton Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Utiliser le jeton pour demander les ressources de runtime LUIS dans les abonnements, à partir de l’[API d’obtention de comptes LUIS Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auxquels votre compte d’utilisateur a accès.

    Cette API POST nécessite les paramètres suivants :

    |En-tête|Valeur|
    |--|--|
    |`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |`Ocp-Apim-Subscription-Key`|Votre clé de création.|

    Cette API retourne un tableau d’objets JSON de vos abonnements LUIS, notamment l’ID d’abonnement, le groupe de ressources et le nom de la ressource, retourné en tant que nom de compte. Recherchez l’élément dans le tableau qui est la ressource LUIS à affecter à l’application LUIS.

1. Affectez le jeton à la ressource LUIS avec l’API d’[affectation de compte LUIS Azure à une application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515).

    Cette API POST nécessite les paramètres suivants :

    |Type|Paramètre|Valeur|
    |--|--|--|
    |En-tête|`Authorization`|La valeur de `Authorization` est `Bearer {token}`. Notez que la valeur du jeton doit être précédée du mot `Bearer` et d’un espace.|
    |En-tête|`Ocp-Apim-Subscription-Key`|Votre clé de création.|
    |En-tête|`Content-type`|`application/json`|
    |Querystring|`appid`|L’ID d’application LUIS.
    |body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quand cette API réussit, elle retourne l’état 201 - créé.

## <a name="unassign-resource"></a>Désaffecter la ressource

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis sélectionnez une application dans la liste **Mes applications**.
1. Accédez à la page **Gérer -> Ressources Azure**.
1. Sélectionnez l’onglet de ressource Prédiction ou Création, puis le bouton **Annuler l’affectation d’une ou plusieurs ressources** pour la ressource.

Lorsque vous annulez l’attribution d’une ressource, celle-ci n’est pas supprimée d’Azure. Elle n’est simplement plus liée à LUIS.

## <a name="reset-authoring-key"></a>Réinitialiser la clé de création

**Pour les applications [migrées de ressource de création](luis-migration-authoring.md)**  : si votre clé de création est compromise, réinitialisez-la dans le portail Azure via la page **Clés** pour cette ressource de création.

**Pour les applications non encore migrées** : la clé est réinitialisée sur toutes vos applications dans le portail LUIS. Si vous créez vos applications à l’aide des API de création, vous devez changer la valeur de Ocp-Apim-Subscription-Key en la définissant sur la nouvelle clé.

## <a name="regenerate-azure-key"></a>Régénérer une clé Azure

Régénérez les clés Azure à partir du portail Azure via la page **Clés**.

## <a name="delete-account"></a>Supprimer le compte

Consultez la page [Stockage et suppression de données](luis-concept-data-storage.md#accounts) pour savoir quelles données sont supprimées lorsque vous supprimez votre compte.

## <a name="change-pricing-tier"></a>Changer le niveau tarifaire

1.  Dans [Azure](https://portal.azure.com), recherchez votre abonnement LUIS. Sélectionnez l’abonnement LUIS.
    ![Rechercher votre abonnement LUIS](./media/luis-usage-tiers/find.png)
1.  Sélectionnez **Niveau tarifaire** pour afficher les niveaux tarifaires disponibles.
    ![Afficher les niveaux tarifaires](./media/luis-usage-tiers/subscription.png)
1.  Sélectionnez le niveau tarifaire, puis choisissez **Sélectionner** pour enregistrer le changement apporté.
    ![Modifier votre niveau de paiement LUIS](./media/luis-usage-tiers/plans.png)
1.  Lorsque la modification de tarification est terminée, une fenêtre contextuelle vérifie le nouveau niveau tarifaire.
    ![Vérifier votre niveau de paiement LUIS](./media/luis-usage-tiers/updated.png)
1. N’oubliez pas d’[affecter cette clé de point de terminaison](#assign-a-resource-to-an-app) sur la page **Publier** et de l’utiliser dans toutes les requêtes de point de terminaison.

## <a name="viewing-azure-resource-metrics"></a>Affichage des métriques de ressources Azure

### <a name="viewing-azure-resource-summary-usage"></a>Affichage du récapitulatif d’utilisation des ressources Azure
Vous pouvez afficher des informations sur l’utilisation de LUIS dans Azure. La page **Vue d’ensemble** affiche des informations récapitulatives récentes, y compris les appels et les erreurs. Si vous effectuez une requête de point de terminaison LUIS, puis consultez immédiatement la **page Vue d’ensemble**, attendez cinq minutes, le temps que l’utilisation s’affiche.

![Affichage résumé de l’utilisation](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personnalisation des graphiques d’utilisation des ressources Azure
Les métriques fournissent une vue plus détaillée des données.

![Mesures par défaut](./media/luis-usage-tiers/metrics-default.png)

Vous pouvez configurer vos graphiques de métriques pour la période et le type de métrique.

![Mesures personnalisées](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerte de seuil de nombre total de transactions
Si vous souhaitez connaître le moment où vous avez atteint un certain seuil de transactions (10 000 transactions, par exemple), vous pouvez créer une alerte.

![Alertes par défaut](./media/luis-usage-tiers/alert-default.png)

Ajout d’une alerte de métrique pour le **nombre total d’appels** pendant une certaine période. Ajouter les adresses de messagerie de toutes les personnes qui doivent recevoir l’alerte. Ajoutez des webhooks pour tous les systèmes qui doivent recevoir l’alerte. Vous pouvez également exécuter une application logique lorsque l’alerte est déclenchée.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment utiliser des versions](luis-how-to-manage-versions.md) pour contrôler le cycle de vie de votre application.
* Découvrez les concepts de [ressource de création](luis-concept-keys.md#authoring-key) et de [contributeurs](luis-concept-keys.md#contributions-from-other-authors).
* Découvrez [comment créer](luis-how-to-azure-subscription.md) des ressources de création et de runtime.
* Migrez vers la nouvelle [ressource de création](luis-migration-authoring.md).
