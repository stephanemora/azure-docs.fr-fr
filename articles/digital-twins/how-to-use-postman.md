---
title: Faire des demandes avec Postman
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer et utiliser Postman pour tester les API Azure Digital Twins.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588001"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Comment utiliser Postman pour envoyer des demandes aux API Azure Digital Twins

[Postman](https://www.getpostman.com/) est un outil de test REST qui fournit des fonctionnalités clés de requête HTTP dans une interface utilisateur graphique Bureau basée sur un plug-in. Vous pouvez l’utiliser pour créer des requêtes HTTP et envoyer celles-ci aux [API REST Azure Digital Twins](how-to-use-apis-sdks.md).

Cet article décrit comment configurer le [client REST Postman](https://www.getpostman.com/) pour interagir avec les API Azure Digital Twins en procédant comme suit :

1. Utilisez l’interface de ligne de commande Azure pour [**obtenir un jeton du porteur**](#get-bearer-token) permettant d’effectuer des demandes API dans Postman.
1. Créez une [**collection Postman**](#about-postman-collections) et configurez le client REST Postman afin qu’il utilise votre jeton du porteur pour s’authentifier. Lors de la configuration de la collection, vous avez le choix entre les options suivantes :
    1. [**Importer**](#import-collection-of-azure-digital-twins-apis) une collection prédéfinie de demandes API Azure Digital Twins ;
    1. [**Créer**](#create-your-own-collection) votre propre collection à partir de rien.
1. [**Ajoutez des demandes**](#add-an-individual-request) à votre collection configurée, et envoyez-les aux API Azure Digital Twins.

Azure Digital Twins comprend deux ensembles API que vous pouvez utiliser : un **plan de données** et un **plan de contrôle**. Pour plus d’informations sur la différence entre ces ensembles API, consultez [*Guide pratique : Utiliser les API et les Kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md). Cet article contient des informations sur les deux ensembles API.

## <a name="prerequisites"></a>Prérequis

Pour utiliser Postman pour accéder aux API Azure Digital Twins, vous devez configurer une instance Azure Digital Twins et télécharger Postman. Le reste de cette section vous guidera dans ces étapes.

### <a name="set-up-azure-digital-twins-instance"></a>Configurer l’instance Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Téléchargez Postman.

Téléchargez ensuite la version de bureau du client Postman. Accédez à [*www.getpostman.com/apps*](https://www.getpostman.com/apps) et suivez les invites pour télécharger l’application.

## <a name="get-bearer-token"></a>Obtenir un jeton du porteur

À présent que vous avez configuré Postman et votre instance Azure Digital Twins, vous devez obtenir un jeton du porteur que les demandes Postman peuvent utiliser pour autoriser les API Azure Digital Twins.

Il existe plusieurs façons d’obtenir ce jeton. Dans le cadre de cet article, vous allez utiliser l’[interface de ligne de commande Azure](/cli/azure/install-azure-cli) pour vous connecter à votre compte Azure et obtenir un jeton de cette façon.

Si vous disposez d’une interface de ligne de commande Azure [installée localement](/cli/azure/install-azure-cli), vous pouvez démarrer une invite de commandes sur votre ordinateur pour exécuter les commandes suivantes.
Dans le cas contraire, vous pouvez ouvrir une fenêtre [Azure Cloud Shell](https://shell.azure.com) dans votre navigateur et y exécuter les commandes.

1. Tout d’abord, assurez-vous que vous êtes connecté à Azure avec les informations d’identification appropriées, en exécutant la commande suivante :

    ```azurecli-interactive
    az login
    ```

2. Ensuite, utilisez la commande [az account get-access-token](/cli/azure/account#az_account_get_access_token) pour obtenir un jeton du porteur ayant accès au service Azure Digital Twins. Dans cette commande, vous allez transmettre l’ID de ressource pour le point de terminaison de service Azure Digital Twins afin d’obtenir un jeton d’accès permettant d’accéder aux ressources Azure Digital Twins. 

    Le contexte requis pour le jeton dépend de l’ensemble API que vous utilisez. Utilisez donc les onglets ci-dessous pour choisir entre les API de plan de [données](how-to-use-apis-sdks.md#overview-data-plane-apis) et de [plan de contrôle](how-to-use-apis-sdks.md#overview-control-plane-apis) .

    # <a name="data-plane"></a>[Plan de données](#tab/data-plane)
    
    Pour obtenir un jeton à utiliser avec les API de **plan de données**, utilisez la valeur statique suivante comme contexte du jeton : `0b07f429-9f4b-4714-9392-cc5e8e80c8b0`. Il s’agit de l’ID de ressource du le point de terminaison de service Azure Digital Twins.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Plan de contrôle](#tab/control-plane)
    
    Pour obtenir un jeton à utiliser avec les API de **plan de contrôle**, utilisez la valeur suivante comme contexte du jeton : `https://management.azure.com/`.
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Si vous avez besoin d’accéder à votre instance Azure Digital Twins à l’aide d’un principal de service ou d’un compte d’utilisateur qui appartient à un autre locataire Azure Active Directory de l’instance, vous devez demander un **jeton** au locataire « d’accueil » de l’instance Azure Digital Twins. Pour plus d’informations sur ce processus, consultez le [*Guide pratique : écrire du code d’authentification d’application*](how-to-authenticate-client.md#authenticate-across-tenants).

3. Copiez la valeur de `accessToken` dans le résultat, puis enregistrez-la pour l’utiliser dans la section suivante. Il s’agit de la **valeur de jeton** que vous allez fournir à Postman pour autoriser vos demandes.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Capture d’écran de la console montrant le résultat de la commande az account-access-token. Le champ accessToken et son exemple de valeur sont mis en évidence.":::

>[!TIP]
>Ce jeton est valide pendant au minimum cinq minutes et au maximum 60 minutes. Si le temps alloué ne suffit pas pour le jeton actuel, vous pouvez répéter les étapes décrites dans cette section pour en obtenir un nouveau.

Ensuite, vous allez configurer Postman afin d’utiliser ce jeton pour envoyer des demandes API à Azure Digital Twins.

## <a name="about-postman-collections"></a>À propos des collections Postman

Les demandes dans Postman sont enregistrées dans des **collections** (groupes de demandes). Lorsque vous créez une collection pour regrouper vos demandes, vous pouvez appliquer des paramètres communs à de nombreuses demandes à la fois. Cela peut considérablement simplifier l’autorisation si vous envisagez de créer plusieurs demandes d’API Azure Digital Twins, car vous ne devez configurer ces détails qu’une seule fois pour l’ensemble de la collection.

Quand utilisez Azure Digital Twins, vous pouvez commencer par importer une [collection prédéfinie de toutes les demandes Azure Digital Twins](#import-collection-of-azure-digital-twins-apis). Cela est utile si vous explorez les API et souhaitez rapidement configurer un projet avec des exemples de demandes.

Vous pouvez également choisir de commencer à partir de rien en [créant votre propre collection vide](#create-your-own-collection), puis en la remplissant de demandes individuelles qui appellent uniquement les API dont vous avez besoin. 

Les sections suivantes décrivent les deux processus. Le reste de l’article ayant trait à votre application Postman locale, vous devez ouvrir l’application sur votre ordinateur maintenant.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importer une collection d’API Azure Digital Twins

Un moyen rapide de prendre en main Azure Digital Twins dans Postman consiste à importer une collection prédéfinie de demandes pour les API Azure Digital Twins.

### <a name="download-the-collection-file"></a>Télécharger le fichier de la collection

La première étape de l’importation de l’ensemble API consiste à télécharger une collection. Choisissez ci-dessous l’onglet correspondant à votre choix de plan de données ou de plan de contrôle pour voir les options de collection prédéfinie.

# <a name="data-plane"></a>[Plan de données](#tab/data-plane)

Vous avez le choix entre deux collections de plan de données Azure Digital Twins :
* [**Collection Postman Azure Digital Twins**](https://github.com/microsoft/azure-digital-twins-postman-samples) : cette collection offre une expérience de prise en main simple d’Azure Digital Twins dans Postman. Les demandes incluant des exemples de données, vous pouvez les exécuter moyennant un minimum de modifications. Choisissez cette collection si vous souhaitez disposer d’un ensemble gérable de demandes API clés contenant des exemples d’informations.
    - Pour trouver la collection, suivez le lien du référentiel, puis ouvrez le fichier nommé *postman_collection.json*.
* **[Swagger de plan de données Azure Digital Twins](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** : ce référentiel contient le fichier Swagger complet de l’ensemble API Azure Digital Twins, que vous pouvez télécharger et importer dans Postman en tant que collection. Celui-ci fournit un ensemble complet de demandes API, mais avec des corps de données vides plutôt que des exemples de données. Choisissez cette collection si vous souhaitez avoir accès à chaque appel d’API et entrer toutes les données vous-même.
    - Pour trouver la collection, suivez le lien du référentiel et choisissez le dossier correspondant à la version de spécification la plus récente. À partir de là, ouvrez le fichier appelé *digitaltwins.json*.

# <a name="control-plane"></a>[Plan de contrôle](#tab/control-plane)

La collection actuellement disponible pour le plan de contrôle est le [**Swagger de plan de contrôle Azure Digital Twins**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ce référentiel contient le fichier Swagger complet pour l’ensemble API Azure Digital Twins que vous pouvez télécharger et importer dans Postman en tant que collection. Vous disposerez ainsi d’un ensemble complet de chaque demande API.

Pour trouver la collection, suivez le lien du référentiel et choisissez le dossier correspondant à la version de spécification la plus récente. À partir de là, ouvrez le fichier appelé *digitaltwins.json*.

---

Voici comment télécharger la collection choisie sur votre ordinateur afin de pouvoir l’importer dans Postman.
1. Utilisez les liens ci-dessus pour ouvrir le fichier de collection dans GitHub dans votre navigateur.
1. Sélectionnez le bouton **Brut** pour ouvrir le texte brut du fichier.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Capture d’écran du fichier digitaltwins.json de plan de données dans GitHub. Le bouton Brut est en évidence." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Copiez le texte de la fenêtre, puis collez-le dans un nouveau fichier sur votre ordinateur.
1. Enregistrez le fichier avec une extension *.json* (vous pouvez le nommer à votre guise, pour autant que vous puissiez mémoriser son nom afin de le retrouver ultérieurement).

### <a name="import-the-collection"></a>Importer la collection

Ensuite, importez la collection dans Postman.

1. Dans la fenêtre principale de Postman, sélectionnez le bouton **Importer**.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Capture d’écran d’une fenêtre de Postman venant de s’ouvrir, avec le bouton « Importer » en évidence" lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Dans la fenêtre Importer qui suit, sélectionnez **Charger des fichiers** et accédez sur votre ordinateur au fichier de collection que vous avez créé précédemment. Sélectionnez Ouvrir.
1. Sélectionnez le bouton **Importer** pour confirmer.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Capture d’écran de la fenêtre « Importer » de Postman, montrant le fichier à importer en tant que collection et le bouton Importer.":::

La collection importée est visible dans l’affichage principal de Postman, sous l’onglet Collections.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Capture d’écran de la fenêtre principale de Postman, avec la collection que vous venez de créer est mise en évidence sous l’onglet « Collections »." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Ensuite, passez à la section suivante pour ajouter un jeton du porteur à la collection pour l’autorisation, et le connecter à votre instance Azure Digital Twins.

### <a name="configure-authorization"></a>Configurer une autorisation

Ensuite, modifiez la collection que vous avez créée afin de configurer des détails d’accès. Mettez en surbrillance la collection que vous avez créée, puis sélectionnez l’icône **Afficher d’autres actions** pour afficher un menu. Sélectionnez **Modifier**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Capture d’écran de Postman. L’icône « Afficher d’autres actions » pour la collection importée est mise en évidence, ainsi que l’option « Modifier »." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Procédez comme suit pour ajouter un jeton du porteur à la collection pour l’autorisation. C’est là que vous allez utiliser la **valeur de jeton** que vous avez recueillie dans la section [Obtenir un jeton du porteur](#get-bearer-token) afin de l’utiliser pour toutes les demandes API dans votre collection.

1. Dans la boîte de dialogue d’édition de votre collection, vérifiez que vous êtes sous l’onglet **Autorisation** . 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Capture d’écran de la boîte de dialogue de modification de la collection importée dans Postman, montrant l’onglet « Autorisation »." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Définissez le Type sur **OAuth 2.0**, collez votre jeton d’accès dans la zone Jeton d’accès, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Capture d’écran de la boîte de dialogue de modification de Postman pour la collection importée, sous l’onglet « Autorisation ». Le type est « OAuth 2.0 » et la zone Jeton d’accès est mise en évidence." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Configuration supplémentaire

# <a name="data-plane"></a>[Plan de données](#tab/data-plane)

Si vous créez une collection [plan de données](how-to-use-apis-sdks.md#overview-data-plane-apis), aidez-la à se connecter facilement à vos ressources Azure Digital Twins en définissant certaines **variables** fournies avec les collections. Lorsque de nombreuses demandes d’une collection requièrent la même valeur (comme le nom d’hôte de votre instance Azure Digital Twins), vous pouvez stocker la valeur dans une variable s’appliquant à chaque demande de la collection. Les deux collections téléchargeables pour Azure Digital Twins sont fournies avec des variables prêtes à l’emploi que vous pouvez définir au niveau de la collection.

1. Toujours dans la boîte de dialogue de modification de votre collection, accédez à l’onglet **Variables** .

1. Utilisez le **nom d’hôte** de votre instance figurant dans la section [*Conditions préalables*](#prerequisites) pour définir le champ VALEUR ACTUELLE de la variable pertinente. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Capture d’écran de la boîte de dialogue de modification de la collection importée dans Postman, montrant l’onglet « Variables », avec le champ « CURRENT VALUE » (Valeur actuelle) mis en évidence." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Si votre collection comprend des variables supplémentaires, entrez et enregistrez également ces valeurs.

Une fois les étapes ci-dessus accomplies, vous avez fini de configurer la collection. Vous pouvez fermer l’onglet de modification de la collection si vous le souhaitez.

# <a name="control-plane"></a>[Plan de contrôle](#tab/control-plane)

Si vous créez une collection de [plan de contrôle](how-to-use-apis-sdks.md#overview-control-plane-apis), vous avez fait tout ce qu’il fallait pour configurer la collection. Vous pouvez fermer l’onglet de modification de la collection si vous le souhaitez, puis passer à la section suivante.

--- 

### <a name="explore-requests"></a>Explorer les demandes

Ensuite, explorez les demandes à l’intérieur de la collection d’API Azure Digital Twins. Vous pouvez développer la collection pour afficher les demandes prêtes à l’emploi (triées par catégorie d’opération). 

Les différentes demandes requièrent des informations différentes sur votre instance et ses données. Pour afficher toutes les informations requises pour élaborer une demande particulière, recherchez les détails de la demande dans la [documentation de référence sur l’API REST Azure Digital Twins](/rest/api/azure-digitaltwins/).

Vous pouvez modifier les détails d’une demande dans la collection Postman en procédant comme suit :

1. Sélectionnez-la dans la liste pour extraire ses détails modifiables. 

1. Entrez les valeurs des variables répertoriées sous l’onglet **Paramètres**, sous **Variables de chemin d’accès**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Capture d’écran de Postman. La collection est développée pour afficher une demande. La section « Variables de chemin d’accès » est mise en évidence dans les détails de la demande." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Fournissez les détails d’**En-têtes** ou de **Corps** nécessaires dans les onglets respectifs.

Une tous les détails requis fournis, vous pouvez exécuter la demande avec le bouton **Envoyer**.

Vous pouvez également ajouter vos propres demandes à la collection, en procédant de la manière décrite dans la section [*Ajouter une demande individuelle*](#add-an-individual-request) ci-dessous.

## <a name="create-your-own-collection"></a>Créer votre propre collection

Au lieu d’importer la collection existante de toutes les API Azure Digital Twins, vous pouvez créer votre propre collection à partir de rien. Vous pouvez ensuite la remplir de demandes individuelles en procédan de la manière décrite dans la [documentation de référence sur l’API REST Azure Digital Twins](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Créer une collection Postman

1. Pour créer un collection, sélectionnez le bouton **Nouveau** dans la fenêtre principale de Postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Capture d’écran de la fenêtre principale de Postman, avec le bouton « Nouveau » mis en évidence." lightbox="media/how-to-use-postman/postman-new.png":::

    Choisissez un type de **Collection**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Capture d’écran de la boîte de dialogue « Créer » dans Postman, avec l’option « Collection » mise en évidence.":::

1. Un onglet s’ouvre pour l’entrée des détails de la nouvelle collection. Sélectionnez l’icône Modifier en regard du nom par défaut de la collection (**Nouvelle collection**) pour le remplacer par le nom de votre choix. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Capture d’écran de la boîte de dialogue de modification de la nouvelle collection dans Postman, avec l’icône Modifier en regard du nom de la « Nouvelle collection » mise en évidence." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Ensuite, passez à la section suivante pour ajouter un jeton du porteur à la collection à des fins d’autorisation.

### <a name="configure-authorization"></a>Configurer une autorisation

Procédez comme suit pour ajouter un jeton du porteur à la collection pour l’autorisation. C’est là que vous allez utiliser la **valeur de jeton** que vous avez recueillie dans la section [Obtenir un jeton du porteur](#get-bearer-token) afin de l’utiliser pour toutes les demandes API dans votre collection.

1. Toujours dans la boîte de dialogue de modification de votre nouvelle collection, accédez à l’onglet **Autorisation** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Capture d’écran de la boîte de dialogue de modification de la nouvelle collection dans Postman, montrant l’onglet « Autorisation »." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Définissez le Type sur **OAuth 2.0**, collez votre jeton d’accès dans la zone Jeton d’accès, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Capture d’écran de la boîte de dialogue de modification de Postman pour la nouvelle collection, sous l’onglet « Autorisation ». Le type est « OAuth 2.0 » et la zone Jeton d’accès est mise en évidence." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Une fois les étapes ci-dessus accomplies, vous avez fini de configurer la collection. Vous pouvez fermer l’onglet de modification pour la nouvelle collection si vous le souhaitez.

La collection est visible dans l’affichage principal de Postman, sous l’onglet Collections.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Capture d’écran de la fenêtre principale de Postman, avec la collection personnalisée nouvellement créée en évidence sous l’onglet « Collections »." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Ajouter une demande individuelle.

À présent que votre collection est configurée, vous pouvez ajouter vos propres demandes aux API Azure Digital Twins.

1. Pour créer une demande, appuyez utiliser le bouton **Nouveau**.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Capture d’écran de la fenêtre principale de Postman, avec le bouton « Nouveau » mis en évidence." lightbox="media/how-to-use-postman/postman-new.png":::

    Choisissez un type de **Demande**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Capture d’écran de la boîte de dialogue « Créer » dans Postman, avec l’option « Demande » mise en évidence.":::

1. Cette action ouvre la fenêtre ENREGISTRER LA DEMANDE dans laquelle vous pouvez entrer un nom et une description facultative pour votre demande, ainsi que choisir la collection à laquelle elle appartient. Entrez les détails et enregistrez la demande dans la collection que vous avez créée précédemment.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Capture d’écran de la fenêtre « Enregistrer la demande » dans Postman, montrant les champs décrits et le bouton « Enregistrer dans la collection Azure Digital Twins » mis en évidence.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Vous pouvez maintenant afficher votre demande dans la collection, et la sélectionner pour extraire ses détails modifiables.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Capture d’écran de Postman, avec la collection Azure Digital Twins développée pour afficher les détails de la demande." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Définir les détails d’une demande

Pour envoyer une demande Postman à l’une des API Azure Digital Twins, vous avez besoin de l’URL de l’API et d’informations sur les détails requis. Vous trouverez ces informations dans la [documentation de référence sur l’API REST Azure Digital Twins](/rest/api/azure-digitaltwins/).

Pour continuer avec un exemple de requête, cet article utilise l’API de requête (et sa [documentation de référence](/rest/api/digital-twins/dataplane/query/querytwins)) pour interroger tous les jumeaux numériques dans une instance.

1. Trouvez l’URL et le type de la demande dans la documentation de référence. Pour l’API de requête, il s’agit actuellement de *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`* .
1. Dans Postman, définissez le type de la demande, puis entrez son URL en remplissant les espaces réservés dans l’URL de manière appropriée. C’est là que vous allez utiliser le **nom d’hôte** de votre instance, évoqué dans la section [*Conditions préalables*](#prerequisites).
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Capture d’écran des détails de la nouvelle demande dans Postman, avec l’URL de requête de la documentation de référence renseignée dans la zone URL de demande." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Vérifiez que les paramètres indiqués pour la demande sous l’onglet **Params** correspondent à ceux décrits dans la documentation de référence. Pour cette demande dans Postman, le paramètre `api-version` a été automatiquement renseigné lors de l’entrée de l’URL de la demande à l’étape précédente. Pour l’API de requête, comme il s’agit du seul paramètre requis, cette étape est accomplie.
1. Sous l’onglet **Autorisation**, définissez le Type sur **Hériter du parent**. Cela indique que cette demande utilisera l’autorisation que vous avez configurée précédemment pour la collection entière.
1. Vérifiez que les en-têtes affichés pour la demande sous l’onglet **En-têtes** correspondent à ceux décrits dans la documentation de référence. Pour cette demande, plusieurs en-têtes ont été remplis automatiquement. Pour l’API de requête, aucune des options d’en-tête n’étant requise, cette étape est accomplie.
1. Vérifiez que le corps affiché pour la demande sous l’onglet **Corps** correspond à celui décrit dans la documentation de référence. Pour l’API de requête, un corps JSON est requis pour fournir le texte de la requête. Voici un exemple de corps pour cette demande qui interroge tous les jumeaux numériques dans l’instance :

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Capture d’écran des détails de la nouvelle demande dans Postman, sous l’onglet Corps. Celui-ci contient un corps JSON brut avec une requête « SELECT * FROM DIGITALTWINS »." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Pour plus d’informations sur la création de requêtes Azure Digital Twins, consultez [*Guide pratique : Interroger le graphique de jumeaux*](how-to-query-graph.md).

1. Consultez la documentation de référence pour connaître les autres champs susceptibles d’être requis pour votre type de demande. Pour l’API de requête, toutes les exigences ayant été satisfaites dans la demande Postman, cette étape est accomplie.
1. Utilisez le bouton **Envoyer** pour envoyer votre demande terminée.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Capture d’écran de Postman montrant les détails de la nouvelle demande, avec le bouton Envoyer mis en évidence." lightbox="media/how-to-use-postman/postman-request-send.png":::

Après l’envoi de la demande, les détails de la réponse s’affichent dans la fenêtre Postman sous la demande. Vous pouvez afficher le code d’état de la réponse et tout texte de corps.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Capture d’écran de la demande envoyée dans Postman. Sous les détails de la demande, la réponse est affichée. L’État est 200 OK et le corps contient les résultats de la requête." lightbox="media/how-to-use-postman/postman-request-response.png":::

Vous pouvez également comparer la réponse aux données de réponse attendues dans la documentation de référence pour vérifier le résultat ou en savoir plus sur les erreurs qui se produisent.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les API Digital Twins, lisez [*Guide pratique : Utiliser les API et les kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md) ou consultez la [documentation de référence pour les API REST](/rest/api/azure-digitaltwins/).