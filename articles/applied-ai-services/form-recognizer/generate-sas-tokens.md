---
title: Générer des jetons SAS pour les conteneurs et les blobs avec le portail Azure
description: Découvrez comment générer des jetons de signature d’accès partagé (SAS) pour les conteneurs et les blobs dans le portail Azure.
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 70ab13f1ca1eee2d5c01d3aa1af6255bc06aeb3d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233655"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Créer des jetons SAS pour les conteneurs de stockage

Dans cet article, vous allez apprendre à générer des jetons de signature d’accès partagé (SAS) de délégation d’utilisateur pour les conteneurs Stockage Blob Azure. Un jeton SAS de délégation d’utilisateur se connecte avec des informations d’identification Azure Active Directory (Azure AD) au lieu de clés de stockage Azure. Il fournit un accès délégué et sécurisé aux ressources dans votre compte de stockage Azure.

À un niveau élevé, voici comment cela fonctionne : votre application fournit le jeton SAS au stockage Azure dans le cadre d’une requête. Si le service de stockage vérifie que la signature d’accès partagé est valide, la requête est autorisée. Si la signature d’accès partagé est jugée non valide, la requête est refusée avec le code d’erreur 403 (Interdit).

Le Stockage Blob Azure offre trois types de ressources :

* Les comptes de **Stockage** fournissent un espace de noms unique dans Azure pour vos données.
* Les **Conteneurs** se trouvent dans des comptes de stockage et organisent des jeux d’objets blob.
* Les **objets blob** se trouvent dans des conteneurs et stockent du texte et des données binaires.

> [!NOTE]
>
> * Si votre compte de stockage Azure est protégé par un réseau virtuel ou un pare-feu, vous ne pouvez pas accorder l’accès à l’aide d’un jeton SAS. Vous devrez utiliser une [identité managée](managed-identity-byos.md) pour accorder l’accès à votre ressource de stockage.
> * Une [identité managée](managed-identity-byos.md) prend en charge les comptes de Stockage Blob Azure accessibles en privé et publiquement.
>

## <a name="when-to-use-a-shared-access-signature"></a>Quand utiliser une signature d’accès partagé

* Si vous utilisez des conteneurs de stockage avec un accès public, vous pouvez choisir d’utiliser un jeton SAS pour accorder un accès limité à vos ressources de stockage.
* Lorsque vous effectuez l’apprentissage d’un modèle personnalisé, votre ensemble assemblé de documents de formation *doit* être chargé dans un conteneur de Stockage Blob Azure. Vous pouvez accorder l’autorisation à vos ressources de formation à l’aide d’un jeton SAS de délégation d’utilisateur.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [compte Azure](https://azure.microsoft.com/free/cognitive-services/) actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/).
* Une ressource [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure.
* Un [compte de Stockage Blob Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) de **performances standard**. Vous allez créer des conteneurs pour stocker et organiser vos données d’objet blob dans votre compte de stockage. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, suivez les démarrages rapides suivants :

  * [Créer un compte de stockage](../../storage/common/storage-account-create.md). Lorsque vous créez votre compte de stockage, sélectionnez les performances **Standard** dans le champ **Détails de l’instance** > **Performance**.
  * [Créer un conteneur](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). Lors de la création de votre conteneur, définissez le champ **Niveau d’accès public** sur **Conteneur** (accès en lecture anonyme pour les conteneurs et les blobs) dans la fenêtre **Nouveau conteneur**.

## <a name="upload-your-documents"></a>Charger les documents

1. Accédez au [portail Azure](https://ms.portal.azure.com/#home). Sélectionnez **votre compte de stockage** > **Stockage des données** > **Conteneurs**.

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Capture d’écran illustrant le menu Stockage des données dans le portail Azure.":::

1. Sélectionnez un conteneur dans la liste.
1. Sélectionnez **Télécharger** dans le menu en haut de la page.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Capture d’écran illustrant le bouton Charger du conteneur dans le portail Azure.":::

   La fenêtre **Charger l’objet blob** s’affiche.
1. Sélectionnez vos fichiers à charger.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Capture d’écran illustrant la fenêtre Charger l’objet blob dans le portail Azure.":::

> [!NOTE]
> Par défaut, l’API REST utilise des formulaires situés à la racine de votre conteneur. Vous pouvez aussi utiliser des données organisées en sous-dossiers si cela est spécifié dans l’appel de l’API. Pour plus d’informations, consultez [Organiser vos données dans des sous-dossiers](./build-training-data-set.md#organize-your-data-in-subfolders-optional).

## <a name="create-a-shared-access-signature-with-the-azure-portal"></a>Créer une signature d’accès partagé avec le portail Azure

> [!IMPORTANT]
>
> Générez et récupérez la signature d’accès partagé pour votre conteneur, pas pour le compte de stockage lui-même.

1. Dans le [portail Azure](https://ms.portal.azure.com/#home), sélectionnez **Votre compte de stockage** > **Conteneurs**.
1. Sélectionnez un conteneur dans la liste.
1. Accédez à droite de la fenêtre principale et sélectionnez les trois point de suspension associés au conteneur choisi.
1. Sélectionnez **Générer SAS** à partir du menu déroulant pour ouvrir la fenêtre **Générer SAS**.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Capture d’écran illustrant le menu déroulant Générer un jeton SAS dans le portail Azure.":::

1. Sélectionnez **Méthode de signature** > **Clé de délégation d’utilisateur**.

1. Définissez les **Autorisations** en cochant ou en décochant la case appropriée. Assurez-vous que les autorisations **Lecture**, **Ecriture**, **Suppression** et **Liste** sont sélectionnées.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Capture d’écran illustrant les champs d’autorisation SAS dans le portail Azure.":::

    >[!IMPORTANT]
    >
    > * Si vous recevez un message similaire à celui ci-dessous, vous devrez affecter l’accès aux données blob dans votre compte de stockage :
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Capture d’écran illustrant l’avertissement lié à l’absence d’autorisations.":::
    >
     > * Le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md) (Azure RBAC) est le système d’autorisation utilisé pour gérer l’accès aux ressources Azure. Azure RBAC vous permet de gérer l’accès et les autorisations pour vos ressources Azure.
    > * Le guide [Attribuer un rôle Azure pour accéder au données blob](../../role-based-access-control/role-assignments-portal.md?tabs=current) vous montre comment attribuer un rôle qui accorde les autorisations de lecture, d’écriture et de suppression pour votre conteneur de stockage Azure. Par exemple, consultez [Contributeur aux données Stockage Blob Azure](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor).

1. Spécifiez les heures de **début** et d’**expiration** de la clé signée. La valeur de l’heure d’expiration est un maximum de sept jours à partir du début de la signature d’accès partagé.

1. Le champ **Adresses IP autorisées** est facultatif. Il spécifie une adresse IP ou une plage d’adresses IP à partir desquelles des requêtes doivent être acceptées. Si l’adresse IP de la requête e ne correspond pas à l’adresse IP ou à la plage d’adresses spécifiée sur le jeton SAS, elle ne sera pas autorisée.

1. Le champ **Protocoles autorisés** est facultatif. Il spécifie le protocole autorisé pour une requête effectuée avec la signature d’accès partagé. La valeur par défaut est HTTPS.

1. Sélectionnez **Générer un jeton SAS et une URL**.

1. La chaîne de requête **Jeton SAS blob** et l’**URL SAS blob** s’affichent en bas dans la fenêtre. Pour utiliser le jeton SAS blob, ajoutez-le à un URI de service de stockage.

1. Copiez et collez les valeurs **Jeton SAS blob** et **URL SAS blob** en lieu sûr. Elles ne s’affichent qu’une seule fois et ne peuvent pas être récupérées après la fermeture de la fenêtre.

## <a name="create-a-shared-access-signature-with-the-azure-cli"></a>Créer une signature d’accès partagé avec Azure CLI

1. Pour créer une signature SAS de délégation utilisateur pour un conteneur à l’aide de l’interface CLI Azure, assurez-vous d’avoir installé la version 2.0.78 ou ultérieure. Utilisez la commande `az --version` pour vérifier la version installée.

1. Appelez la commande [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) .

1. Les paramètres suivants sont requis :

    * `auth-mode login`. Ce paramètre garantit que les requêtes effectuées auprès du Stockage Azure sont autorisées avec vos informations d’identification Azure AD.
    * `as-user`. Ce paramètre indique que la signature SAS générée est une SAS de délégation d’utilisateur.

1. Les autorisations prises en charge pour une signature SAS sur un conteneur incluent Ajouter (a), Créer (c), Supprimer (d), Répertorier (l), Lire (r) et Écrire (w). Assurez-vous que **r**, **w**, **d** et **l** sont inclus dans les paramètres des autorisations.

1. Lorsque vous créez une signature SAS de délégation utilisateur avec une interface CLI Azure, l’intervalle maximal pendant lequel la clé de délégation d’utilisateur est valide est de sept jours à partir de la date de début. Spécifiez un délai d’expiration pour la signature d’accès partagé qui se situe dans les sept jours suivants l’heure de début. Pour plus d’informations, consultez [Créer une signature SAS de délégation d’utilisateur pour un conteneur ou un blob avec l’interface CLI Azure](../../storage/blobs/storage-blob-user-delegation-sas-create-cli.md#use-azure-ad-credentials-to-secure-a-sas).

### <a name="example"></a> Exemple

Générer une signature SAS de délégation utilisateur. Remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="use-your-blob-sas-url"></a>Utiliser votre URL SAS blob

Deux options sont disponibles :

* Pour utiliser l’URL SAS de votre objet blob avec l'[API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync), ajoutez l’URL SAS au corps de la demande :

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Pour utiliser votre URL SAS Blob avec l’[outil d’étiquetage Form Recognizer](https://fott-2-1.azurewebsites.net/connections/create), ajoutez l’URL SAS au champ **Paramètres de connexions** > **Conteneur blob Azure** > **URI SAS** :

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="Capture d’écran illustrant le champ URI SAS.":::

Vous avez terminé. Vous avez appris à générer des jetons SAS pour autoriser la manière dont les clients accèdent à vos données.

## <a name="next-step"></a>Étape suivante

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](build-training-data-set.md)