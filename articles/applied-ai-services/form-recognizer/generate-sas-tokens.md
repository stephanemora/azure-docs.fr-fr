---
title: Créer un jeton de signature d’accès partagé (SAS) pour les conteneurs et les blobs avec le portail Azure.
description: Comment créer un Jeton de signature d’accès partagé (SAS) pour les conteneurs et les blobs dans le portail Azure.
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 22fe82425285a27d224ea5375bfe209e7ac779da
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700943"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Créer des jetons SAS pour les conteneurs de stockage

 Dans cet article, vous allez apprendre à générer des jetons de signature d’accès partagé (SAS) de délégation d’utilisateur. Un jeton SAS de délégation d’utilisateur se connecte avec des informations d’identification du Répertoire actif Azure (Azure AD) au lieu de clés de stockage Azure. Il fournit un accès délégué et sécurisé aux ressources dans votre compte de stockage Azure.
À un niveau élevé, voici comment cela fonctionne : votre application fournit le jeton SAS au stockage Azure dans le cadre d’une requête. Si le service de stockage vérifie que la signature SAS est valide, la requête est autorisée. Si la signature SAS est jugée non valide, la requête est refusée avec le code d’erreur 403 (Interdit).

Le stockage Blob Azure offre trois types de ressources :

* Les comptes de **Stockage** fournissent un espace de noms unique dans Azure pour vos données.
* Les **Conteneurs** se trouvent dans des comptes de stockage et organisent des jeux d’objets blob.
* Les **objets blob** se trouvent dans des conteneurs et stockent du texte et des données binaires.

> [!NOTE]
>
> * Si votre compte de stockage Azure est protégé par un Réseau virtuel (VNet) ou un pare-feu, vous ne pouvez pas accorder l’accès à l’aide d’un jeton SAS. Vous devrez utiliser une [**identité managée**](managed-identity-byos.md) pour accorder l’accès à votre ressource de stockage.
>
> * [**Une identité managée**](managed-identity-byos.md) prend en charge les comptes de stockage blob Azure accessibles en privé et publiquement.
>

## <a name="when-to-use-a-shared-access-signature"></a>Quand utiliser une signature d’accès partagé

* Si vous utilisez des conteneurs de stockage avec un accès public, vous pouvez choisir d’utiliser un jeton SAS pour accorder un accès limité à vos ressources de stockage.

* Lorsque vous effectuez l’apprentissage d’un modèle personnalisé, votre ensemble assemblé de documents de formation **doit** être chargé dans un conteneur de stockage d’objets blob Azure. Vous pouvez accorder l’autorisation à vos ressources de formation à l’aide d’un jeton SAS de délégation d’utilisateur.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).
* Une ressource [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [**multiservice Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure.
* Un [**compte de Stockage blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) de **performances standard**. Vous allez créer des conteneurs pour stocker et organiser vos données d’objet blob dans votre compte de stockage. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, suivez les démarrages rapides suivants :

  * [**Créer un compte de stockage**](/azure/storage/common/storage-account-create). Lorsque vous créez votre compte de stockage, veillez à sélectionner performance **Standard** dans le champ **Détails de l’instance → Performance**.
  * [**Créer un conteneur**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). Lors de la création de votre conteneur, définissez le champ **Niveau d’accès public** sur **Conteneur** (accès en lecture anonyme pour les conteneurs et les objets blob) dans la fenêtre **Nouveau conteneur** .

## <a name="upload-your-documents"></a>Charger les documents

1. Accédez au [portail Azure](https://ms.portal.azure.com/#home) et naviguez comme suit : **votre compte de stockage** → **Stockage des données** → **Conteneurs**

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Capture d’écran : menu de stockage des données dans le portail Azure.":::

1. Sélectionnez un **conteneur** dans la liste.
1. Sélectionnez **Télécharger** dans le menu en haut de la page.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Capture d’écran : bouton de chargement du conteneur dans le portail Azure.":::

1. La fenêtre **Télécharger l’objet blob** s’affiche.
1. Sélectionnez le ou les fichiers à charger.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Capture d’écran : fenêtre télécharger l’objet blob dans le portail Azure.":::

> [!NOTE]
> Par défaut, l’API REST utilisera uniquement les formulaires situés à la racine de votre conteneur. Cependant, vous pouvez utiliser des données organisées en sous dossiers si spécifié dans l’appel de l’API. *Consultez*[**Organiser vos données dans des sous-dossiers**](/azure/applied-ai-services/form-recognizer/build-training-data-set.md#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>Créer une signature SAS avec le portail Azure

> [!IMPORTANT]
>
> Générer et récupérer la signature SAS pour votre conteneur, non pour le compte de stockage lui-même.

1. Dans le [portail Azure](https://ms.portal.azure.com/#home), naviguez comme suit :

     **Votre compte de stockage** → **Conteneurs**
1. Sélectionnez un conteneur dans la liste.
1. Accédez à droite de la fenêtre principale et sélectionnez les trois ellipses associées au conteneur choisi.
1. Sélectionnez **Générer une signature SAS** à partir du menu déroulant pour ouvrir la **Fenêtre générer une signature SAS**.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Capture d’écran (portail Azure) : générer un menu déroulant de jeton SAS.":::

1. Sélectionnez **Méthode de signature** → **Clé de délégation d’utilisateur**.

1. Définissez les **Autorisations** en cochant ou en décochant la case appropriée. Assurez-vous que les autorisations **Lecture**, **Ecriture**, **Suppression** et **Liste** sont sélectionnées.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Capture d’écran (portail Azure) : champs d’autorisation SAS.":::

    >[!IMPORTANT]
    >
    > * Si vous recevez un message similaire à celui ci-dessous, vous devez affecter l’accès aux données de l’objet blob dans votre compte de stockage :
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Capture d’écran : avertissement relatif à l’absence d’autorisations.":::
    >
     > * Le [**contrôle d’accès en fonction du rôle Azure**](/azure/role-based-access-control/overview) (Azure RBAC) est le système d’autorisation utilisé pour gérer l’accès aux ressources Azure. Azure RBAC vous permet de gérer l’accès et les autorisations pour vos ressources Azure.
    > * Suivez le guide [**Attribuer un rôle Azure pour accéder au données blob**](/azure/role-based-access-control/role-assignments-portal?tabs=current) pour attribuer un rôle, qui autorise les autorisations de lecture, d’écriture et de suppression pour votre conteneur de stockage Azure, par exemple, [**Contributeur de données blob de stockage**](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor),.

1. Spécifiez les heures de **début** et d’**expiration** de la clé signée. **La valeur de l’heure d’expiration est un maximum de sept jours à partir du début de la signature SAS**.

1. Le champ **Adresses IP autorisées** est facultatif. Il spécifie une adresse IP ou une plage d’adresses IP à partir desquelles des requêtes doivent être acceptées. Si l’adresse IP de la requête e ne correspond pas à l’adresse IP ou à la plage d’adresses spécifiée sur le jeton SAS, elle ne sera pas autorisée.

1. Le champ **Protocoles autorisés** est facultatif. Il spécifie le protocole autorisé pour une requête effectuée avec la signature d’accès partagé. La valeur par défaut est HTTPS.

1. Passez en revue les informations, puis sélectionnez **Générer une URL et un jeton SAS**.

1. La chaîne de requête du **Jeton SAS d’objet blob** et l’**URL SAP d’objet blob** s’affichent en bas dans la fenêtre. Pour utiliser le **jeton SAS d’objet blob**, ajoutez-le à un URI de service de stockage.

1. **Copiez et collez les valeurs Jeton SAS d’objet blob et URL en lieu sûr. Elles ne s’affichent qu’une seule fois et ne peuvent pas être récupérées une fois la fenêtre fermée.**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>Créer une signature SAS avec l’interface de commande en ligne Azure (CLI)

1. Pour créer une signature SAS de délégation utilisateur à l’aide de l’interface CLI Azure, assurez-vous d’avoir installé la version 2.0.78 ou ultérieure. Utilisez la commande `az --version` pour vérifier la version installée.

1. Appelez la commande [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) .

1. Les paramètres suivants sont requis :

    * `auth-mode login`. Ce paramètre garantit que les requêtes effectuées auprès du stockage Azure soient autorisées avec vos informations d’identification Azure AD.
    * `as-user`.  Ce paramètre indique que la signature SAS générée est une SAS de délégation d’utilisateur.

1. Les autorisations prises en charge pour une signature SAS sur un conteneur incluent Ajouter (a), Créer (c), Supprimer (d), Répertorier (l), Lire (r) et Écrire (w).  Assurez-vous que **r**, **w**, **d** et **l** sont inclus dans les paramètres des autorisations.

1. Lorsque vous créez une signature SAS de délégation utilisateur avec une interface CLI Azure, l’intervalle maximal pendant lequel la clé de délégation d’utilisateur est valide est de sept jours à partir de la date de début. Par conséquent, vous devez spécifier un délai d’expiration pour la signature SAS dans les sept jours suivant l’heure de début. *Consultez*[**Créer une signature SAS de délégation d’utilisateur pour un conteneur ou un objet blob avec l’interface CLI Azure**](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a> Exemple

Générer une signature SAS de délégation utilisateur.  Remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>Comment utiliser votre URL SAS de blob

* Pour utiliser l’URL SAS de votre objet blob avec l'[**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync), ajoutez l’URL SAS au corps de la demande :

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Pour utiliser votre **URL SAS Blob** avec l’[**outil d’étiquetage Form Recognizer**](https://fott-2-1.azurewebsites.net/connections/create), ajoutez l’URL SAS au champ **Paramètres de connexions** → **conteneur blob Azure** → **URI SAS** :

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{alt-text}":::

Vous avez terminé. Vous avez appris à générer des jetons SAS pour autoriser la manière dont les clients accèdent à vos données.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](build-training-data-set.md)
