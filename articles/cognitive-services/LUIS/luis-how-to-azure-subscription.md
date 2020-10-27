---
title: Comment utiliser les clés de création et de runtime – LUIS
description: Lorsque vous utilisez Language Understanding (LUIS) pour la première fois, vous n’avez pas besoin de créer de clé de création. Lorsque vous avez l’intention de publier l’application, puis d’utiliser votre point de terminaison de runtime, vous devez créer et attribuer la clé de runtime à l’application.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: adc0c253648ed7ae869a20b92c42e7f6478501b7
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151582"
---
# <a name="create-luis-resources"></a>Créer des ressources LUIS

Les ressources de création et de runtime de prédiction de requête fournissent l’authentification à votre application LUIS et au point de terminaison de prédiction.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Ressources LUIS

LUIS autorise trois types de ressources Azure et une ressource non-Azure :

|Ressource|Objectif|Service cognitif `kind`|Service cognitif `type`|
|--|--|--|--|
|Ressource de création|Vous permet de créer, gérer, entraîner, tester et publier vos applications. [Créez une ressource de création LUIS](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) si vous envisagez de créer des applications LUIS par programmation ou à partir du portail LUIS. Vous devez d’abord [migrer votre compte LUIS](luis-migration-authoring.md#what-is-migration) pour être en mesure de lier vos ressources de création Azure à votre application. Vous pouvez contrôler les autorisations d’accès à la ressource de création en attribuant le [rôle de contributeur](#contributions-from-other-authors) à des utilisateurs. <br><br> Il existe un seul niveau disponible pour la ressource de création LUIS :<br> * **Ressource de création F0 gratuite** qui vous offre 1 million de transactions de création gratuites et 1 000 demandes de point de terminaison de prédiction de test gratuites par mois. |`LUIS.Authoring`|`Cognitive Services`|
|Ressource de prédiction| Après avoir publié votre application LUIS, utilisez la ressource/clé de prédiction pour interroger les demandes de point de terminaison de prédiction. Créez une ressource de prédiction LUIS avant que votre application cliente demande des prédictions au-delà des 1 000 demandes fournies par la ressource de création ou de démarrage. <br><br> Il existe deux niveaux disponibles pour la ressource de prédiction :<br> * **Ressource de prédiction F0 gratuite** qui vous offre 10 000 demandes de point de terminaison de prédiction gratuites par mois<br> * **Ressource de prédiction S0 standard** qui correspond au niveau payant. [En savoir plus sur les détails de la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Ressource de démarrage/d’essai|Vous permet de créer, gérer, entraîner, tester et publier vos applications. Elle est créée par défaut si vous choisissez l’option de ressource de démarrage lors de la première inscription à LUIS. Toutefois, la clé de démarrage finira par être obsolète et tous les utilisateurs de LUIS devront [migrer leurs comptes](luis-migration-authoring.md#what-is-migration) et lier leurs applications LUIS à une ressource de création. Cette ressource ne vous donne pas les autorisations nécessaires pour le contrôle d’accès en fonction du rôle Azure comme la ressource de création. <br><br> Tout comme la ressource de création, la ressource de démarrage vous offre 1 million de transactions de création gratuites et 1 000 demandes de point de terminaison de prédiction de test gratuites.|-|Pas une ressource Azure|
|[Clé de ressource multi-service Cognitive Services](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Requêtes de point de terminaison de prédiction de requête partagées avec LUIS et d’autres services Cognitive Services pris en charge.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Il existe deux types de ressources F0 (niveau gratuit) fournis par LUIS. L’un pour les transactions de création et l’autre pour les transactions de prédiction. Si vous épuisez le quota gratuit de transactions de prédiction, veillez à utiliser en fait la ressource de prédiction F0 qui vous offre 10 000 transactions gratuites par mois et non pas la ressource de création qui vous offre seulement 1 000 transactions de prédiction par mois.

À la fin du processus de création de ressource Azure, [affectez la ressource](#assign-a-resource-to-an-app) à l’application dans le portail LUIS.

Il est important de créer des applications LUIS dans les [régions](luis-reference-regions.md#publishing-regions) où vous souhaitez publier et interroger.

## <a name="resource-ownership"></a>Propriété des ressources

Une ressource Azure, telle qu’une ressource LUIS, appartient à l’abonnement qui contient la ressource.

Pour transférer la propriété d’une ressource, vous pouvez :
* Transférer la [propriété](../../cost-management-billing/manage/billing-subscription-transfer.md) de votre abonnement
* Exporter l’application LUIS en tant que fichier, puis importez l’application sur un autre abonnement. L’exportation est disponible à partir de la page **Mes applications** dans le portail LUIS.


## <a name="resource-limits"></a>Limites des ressources

### <a name="authoring-key-creation-limits"></a>Limites de création de clés de création

Vous pouvez générer jusqu'à 10 clés de création par région et par abonnement.

Consultez [Limites de clés](luis-limits.md#key-limits) et [Régions Azure](luis-reference-regions.md).

Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication dans laquelle vous souhaitez que votre application cliente soit située.

### <a name="key-usage-limit-errors"></a>Erreurs de limite d’utilisation de clé

Les limites d’utilisation dépendent du niveau tarifaire.

Si vous dépassez votre quota de transactions par seconde (TPS), vous recevez une erreur HTTP 429. Si vous dépassez votre quota de transactions par mois (TPM), vous recevez une erreur HTTP 403.


### <a name="reset-authoring-key"></a>Réinitialiser la clé de création

Pour les applications [migrées de ressource de création](luis-migration-authoring.md) : si votre clé de création est compromise, réinitialisez-la dans le portail Azure, dans la page **Clés** pour cette ressource de création.

Pour les applications non encore migrées : la clé est réinitialisée sur toutes vos applications dans le portail LUIS. Si vous créez vos applications à l’aide des API de création, vous devez changer la valeur de Ocp-Apim-Subscription-Key en la définissant sur la nouvelle clé.

### <a name="regenerate-azure-key"></a>Régénérer une clé Azure

Régénérez les clés Azure à partir du portail Azure via la page **Clés** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Propriété, accès et sécurité des applications

Une application est définie par ses ressources Azure, qui sont elles-mêmes déterminées par l'abonnement du propriétaire.

Vous pouvez déplacer votre application LUIS. Utilisez les ressources documentaires suivantes à partir du portail Azure ou de l'interface Azure CLI :

* [Déplacer l'application entre des ressources de création LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Déplacer des ressources au sein d'un même abonnement ou entre des abonnements différents](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contributions d'autres auteurs

Pour les applications [migrées de ressource de création](luis-migration-authoring.md) : les _contributeurs_ sont gérés dans le portail Azure pour la ressource de création, à l’aide de la page **Contrôle d’accès (IAM)** . Apprenez à [ajouter un utilisateur](luis-how-to-collaborate.md) à l'aide de l'adresse e-mail du collaborateur et du rôle de _contributeur_ .

Pour les applications qui n’ont pas encore été migrées : tous les _collaborateurs_ sont gérés dans le portail LUIS à partir de la page **Gérer -> Collaborateurs** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Accès aux prédictions de requête pour les applications privées et publiques

Pour une application **privée** , le runtime de prédiction de requête est accessible aux propriétaires et aux contributeurs. Pour une application **publique** , l'environnement d'exécution est accessible à tous les utilisateurs qui disposent de leur propre ressource d'exécution Azure [Cognitive Service](../cognitive-services-apis-create-account.md) ou [LUIS](#create-resources-in-the-azure-portal) et de l'ID de l'application publique.

Aucun catalogue d'applications publiques n'est actuellement disponible.

### <a name="authoring-permissions-and-access"></a>Autorisations et accès à la création
L'accès à l'application à partir du portail [LUIS](luis-reference-regions.md#luis-website) ou des [API de création](https://go.microsoft.com/fwlink/?linkid=2092087) est contrôlé par la ressource de création Azure.

Le propriétaire et tous les contributeurs disposent de l'accès requis pour créer l'application.

|L’accès à la création inclut|Notes|
|--|--|
|Ajouter ou supprimer des clés de point de terminaison||
|Exporter la version||
|Exporter les journaux d’activité du point de terminaison||
|Importer la version||
|Rendre une application publique|Lorsqu’une application est publique, toute personne disposant d’une clé de création ou de point de terminaison peut interroger l’application.|
|Modifier le modèle|
|Publish|
|Passez en revue les énoncés de point de terminaison pour un [apprentissage actif](luis-how-to-review-endpoint-utterances.md)|
|Former|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Accès à l'environnement d'exécution du point de terminaison de prédiction

L'accès permettant d'interroger le point de terminaison de prédiction est contrôlé par un paramètre de la page **Informations sur l'application** dans la section **Gérer** .

|[Point de terminaison privé](#runtime-security-for-private-apps)|[Point de terminaison public](#runtime-security-for-public-apps)|
|:--|:--|
|Accessible au propriétaire et aux contributeurs|Accessible au propriétaire, aux contributeurs et à toute personne qui connaît l'ID de l'application|

Vous pouvez contrôler qui voit votre clé d'exécution LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Une fois l'API côté serveur appelée et authentifiée, et l'autorisation vérifiée, passez l'appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre clé et l’URL du point de terminaison à vos utilisateurs, vous permet d’effectuer un suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Sécurité d'exécution pour les applications privées

L'environnement d'exécution d'une application privée est uniquement disponible pour :

|Clé et utilisateur|Explication|
|--|--|
|Clé de création du propriétaire| Jusqu’à 1 000 accès au point de terminaison|
|Clés de création collaborateur/contributeur| Jusqu’à 1 000 accès au point de terminaison|
|N'importe quelle clé attribuée à LUIS par un auteur ou un collaborateur/contributeur|Dépend du niveau d’utilisation de la clé|

### <a name="runtime-security-for-public-apps"></a>Sécurité d'exécution pour les applications publiques

Une fois qu’une application est configurée comme étant publique, _toute_ clé de création LUIS valide ou clé de point de terminaison LUIS peut interroger votre application, tant que la clé n’a pas épuisé le quota de point de terminaison.

Un utilisateur qui n'est pas propriétaire ou contributeur ne peut accéder à l'environnement d'exécution d'une application publique que s'il dispose de l'ID de l'application. LUIS ne dispose pas d’un _marché_ public, ni d’un autre moyen de rechercher une application publique.

Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à la clé de ressource.


### <a name="securing-the-query-prediction-endpoint"></a>Sécurisation du point de terminaison de prédiction de requête

Vous pouvez déterminer qui peut voir votre clé d'exécution de point de terminaison de prédiction LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Lorsque l’API côté serveur est appelée et que l’authentification et l’autorisation sont vérifiées, passez l’appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre point de terminaison à vos utilisateurs, vous permet d’effectuer le suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Se connecter au portail LUIS et commencer à créer

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et acceptez les conditions d’utilisation.
1. Commencez votre application LUIS en choisissant votre clé de création Azure LUIS.

   ![Choisir un type de ressource de création Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Une fois le processus de sélection des ressources terminé, [créez une application](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Créer des ressources dans Azure CLI

Utilisez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour créer chaque ressource individuellement.

Ressource `kind` :

* Création : `LUIS.Authoring`
* Prédiction : `LUIS`

1. Connectez-vous à Azure CLI :

    ```azurecli
    az login
    ```

    Cette action ouvre un navigateur qui vous permet de sélectionner le bon compte et de fournir une authentification.

1. Créez une **ressource de création LUIS** , de type `LUIS.Authoring`, nommée `my-luis-authoring-resource` dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Créez une **ressource de point de terminaison de prédiction LUIS** , de type `LUIS`, nommée `my-luis-prediction-resource` dans le groupe de ressources _existant_ nommé `my-resource-group` pour la région `westus`. Si vous recherchez un débit plus élevé que le niveau gratuit, passez de `F0` à `S0`. En savoir plus sur les [niveaux tarifaires et le débit](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Ces clés ne sont **pas** utilisées par le portail LUIS tant qu’elles n’ont pas été attribuées dans le portail LUIS via le menu **Gérer -> Ressources Azure** .

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Affecter une ressource dans le portail LUIS

Vous pouvez affecter une ressource de création pour une seule application ou pour toutes les applications dans LUIS. La procédure suivante affecte toutes les applications à une seule ressource de création.

1. Connectez-vous au [portail LUIS](https://www.luis.ai).
1. Dans la barre de navigation supérieure, tout à fait à droite, sélectionnez votre compte d’utilisateur, puis sélectionnez **Paramètres** .
1. Dans la page **Paramètres utilisateur** , sélectionnez **Ajouter une ressource de création** , puis sélectionnez une ressource de création existante. Sélectionnez **Enregistrer** .

## <a name="assign-a-resource-to-an-app"></a>Affecter une ressource à une application

Notez que si vous ne disposez pas d’un abonnement Azure, vous ne serez pas en mesure d’affecter ni de créer une nouvelle ressource. Vous devez d’abord créer un [essai gratuit Azure](https://azure.microsoft.com/en-us/free/), puis revenir à LUIS pour créer une nouvelle ressource à partir du portail.

Vous pouvez affecter ou créer une ressource de création ou de prédiction dans une application à l’aide de la procédure suivante :

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis sélectionnez une application dans la liste **Mes applications** .
1. Accédez à la page **Gérer -> Ressources Azure** .

    ![Sélectionnez Gérer -> Ressources Azure dans le portail LUIS pour affecter une ressource à l’application.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Sélectionnez l’onglet Ressources de prédiction ou Ressource de création, puis sélectionnez le bouton **Ajouter une ressource de prédiction** ou **Ajouter une ressource de création** .
1. Sélectionnez les champs dans le formulaire pour trouver la ressource correcte, puis sélectionnez **Enregistrer** .
1. Si vous n’avez pas de ressource existante, vous pouvez en créer une en sélectionnant « Créer une nouvelle ressource LUIS ? » en bas de la fenêtre.


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Affecter une ressource de runtime de prédiction de requête sans utiliser le portail LUIS

À des fins d’automation (par exemple pour le pipeline CI/CD), vous pouvez automatiser l’affectation d’une ressource de runtime LUIS à une application LUIS. Pour ce faire, vous devez suivre les étapes ci-dessous :

1. Obtenir un jeton Azure Resource Manager à partir de ce [site web](https://resources.azure.com/api/token?plaintext=true). Ce jeton expire ; veillez donc à l’utiliser immédiatement. La requête retourne un jeton Azure Resource Manager.

    ![Demander un jeton Azure Resource Manager et recevoir un jeton Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Utiliser le jeton pour demander les ressources de runtime LUIS entre différents abonnements, à partir de l’[API d’obtention de comptes LUIS Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auxquelles votre compte d’utilisateur a accès.

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

1. Connectez-vous au [portail LUIS](https://www.luis.ai), puis sélectionnez une application dans la liste **Mes applications** .
1. Accédez à la page **Gérer -> Ressources Azure** .
1. Sélectionnez l’onglet de ressource Prédiction ou Création, puis le bouton **Annuler l’affectation d’une ou plusieurs ressources** pour la ressource.

Lorsque vous annulez l’attribution d’une ressource, celle-ci n’est pas supprimée d’Azure. Elle n’est simplement plus liée à LUIS.


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
1. N’oubliez pas d’ [affecter cette clé de point de terminaison](#assign-a-resource-to-an-app) sur la page **Publier** et de l’utiliser dans toutes les requêtes de point de terminaison.

## <a name="viewing-azure-resource-metrics"></a>Affichage des métriques de ressources Azure

### <a name="viewing-azure-resource-summary-usage"></a>Affichage du récapitulatif d’utilisation des ressources Azure
Vous pouvez afficher des informations sur l’utilisation de LUIS dans Azure. La page **Vue d’ensemble** affiche des informations récapitulatives récentes, y compris les appels et les erreurs. Si vous effectuez une requête de point de terminaison LUIS, puis consultez immédiatement la **page Vue d’ensemble** , attendez cinq minutes, le temps que l’utilisation s’affiche.

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
* Migrez vers la nouvelle [ressource de création](luis-migration-authoring.md).
