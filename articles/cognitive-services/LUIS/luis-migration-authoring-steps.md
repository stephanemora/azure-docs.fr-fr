---
title: Effectuer une migration vers une ressource de création Azure
titleSuffix: Azure Cognitive Services
description: Effectuez une migration vers une ressource de création Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194634"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Étapes d’une migration vers une ressource de création Azure

À partir du portail LUIS, effectuez la migration de toutes les applications que vous avez en vue d’utiliser la ressource de création Azure.

## <a name="prerequisites"></a>Conditions préalables requises

* **Facultatif** : Sauvegardez les applications qui figurent dans la liste des applications du portail LUIS en exportant chaque application ou en utilisant l’[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) d’exportation.
* **Facultatif** : Enregistrez la liste de chaque contributeur de l’application. Tous les collaborateurs peuvent recevoir un e-mail dans le cadre du processus de migration.
* **Obligatoire** : Vous devez disposer d’un [abonnement Azure](https://azure.microsoft.com/free/). Une partie du processus d’abonnement nécessite que vous fournissiez des informations de facturation. Toutefois, vous pouvez utiliser le niveau tarifaire gratuit (F0) quand vous utilisez LUIS. À mesure que votre utilisation augmente,vous pouvez vous rendre compte que vous avez besoin d’un niveau payant.

Si vous n’avez pas d’abonnement Azure, [créez-en un](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Accéder au processus de migration

Chaque semaine, vous êtes invité à effectuer la migration de vos applications. Vous pouvez fermer cette fenêtre pour ne pas effectuer la migration. Si vous souhaitez effectuer la migration avant la prochaine période planifiée, vous pouvez démarrer le processus de migration à l’aide de l’icône **Azure** située dans la barre d’outils supérieure du portail LUIS.

> [!div class="mx-imgBorder"]
> ![Icône Migration](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Le propriétaire de l’application démarre le processus de migration

Vous pouvez démarrer le processus de migration si vous êtes le propriétaire de l’une des applications LUIS.

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et acceptez les conditions d’utilisation.
1. La fenêtre contextuelle de migration vous permet de poursuivre la migration ou de l’effectuer ultérieurement. Sélectionnez **Migrate now** (Effectuer la migration maintenant). Si vous choisissez de la remettre à plus tard, vous disposez de 9 mois pour effectuer la migration vers la nouvelle clé de création dans Azure.

    ![Première fenêtre contextuelle du processus de migration - Sélection de l’option de migration](./media/migrate-authoring-key/migrate-now.png)

1. Si l’une de vos applications est associée à des contributeurs, vous êtes invité à **leur envoyer un e-mail** pour les informer de la migration. Il s’agit d’une étape facultative.

    Après avoir migré votre compte vers Azure, vos applications ne seront plus disponibles pour les collaborateurs.

    Pour chaque collaborateur et application, l’application de messagerie par défaut s’ouvre avec un e-mail légèrement formaté. Vous pouvez modifier l'e-mail avant de l'envoyer.

    Le modèle d'e-mail inclut l'ID exact de l'application et le nom de l'application.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Choisissez de créer une ressource de création LUIS en choisissant d’utiliser une ressource de création existante ou de créer une nouvelle ressource de création.

    > [!div class="mx-imgBorder"]
    > ![Créez une ressource de création](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. Dans la fenêtre suivante, entrez les informations de votre clé de ressource. Après avoir entré les informations, sélectionnez **Create resource** (Créer une ressource). Vous pouvez avoir 10 ressources de création gratuites par région et par abonnement.

    ![Créer une ressource de création](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Quand vous **créez une ressource de création**, fournissez les informations suivantes :

    * **Resource name** (Nom de la ressource) : nom personnalisé que vous choisissez, utilisé comme élément de l’URL pour vos requêtes de point de terminaison de création et de prédiction.
    * **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé.
    * **Subscription name** (Nom de l’abonnement) : abonnement auquel la ressource sera facturée.
    * **Resource group** (Groupe de ressources) : nom de groupe de ressources personnalisé que vous choisissez ou que vous créez. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.
    * **Location** (Emplacement) : le choix de l’emplacement est basé sur la sélection du groupe de ressources (**Resource group)** .
    * **Pricing tier** (Niveau tarifaire) : le niveau tarifaire détermine la transaction maximale par seconde et par mois.

1. Validez votre ressource de création, puis cliquez sur **Migrate now (Migrer maintenant)** .

    ![Créer une ressource de création](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Lorsque la ressource de création est créée, un message de confirmation s’affiche. Sélectionnez **Close** pour fermer la fenêtre contextuelle.

    ![Votre ressource de création a bien été créée.](./media/migrate-authoring-key/migration-success.png)

    La liste **Mes applications** affiche les applications qui ont fait l’objet d’une migration vers la nouvelle ressource de création.

    Vous n’avez pas besoin de connaître la clé de la ressource de création pour continuer à modifier vos applications dans le portail LUIS. Si vous prévoyez de modifier vos applications par programmation, vous aurez besoin des valeurs de la clé de création. Ces valeurs sont affichées dans la page **Gérer -> Ressources Azure** du portail LUIS et sont également disponibles dans la page **Clés** de la ressource du portail Azure.

1. Avant d’accéder à vos applications, sélectionnez l’abonnement et la ressource de création LUIS pour voir les applications que vous pouvez créer.

    ![Sélectionnez un abonnement et une ressource de création LUIS pour voir les applications que vous pouvez créer.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Le contributeur de l’application démarre le processus de migration

Suivez les mêmes étapes que le propriétaire de l’application pour la migration. Le processus crée une nouvelle ressource de création de type `LUIS.Authoring`.

Vous devez migrer votre compte pour l’ajouter en tant que contributeur aux applications migrées appartenant à d’autres utilisateurs.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Après le processus de migration, ajoutez des contributeurs à votre ressource de création.

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Découvrez comment [ajouter des contributeurs](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Dépannage des erreurs lors du processus de migration

Si vous recevez une erreur `MissingSubscriptionRegistration` dans le portail LUIS avec une barre de notification rouge pendant le processus de migration, créez une ressource Cognitive Service dans le portail [Azure](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou avec [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). En savoir plus sur les [causes de cette erreur](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Étapes suivantes


* Découvrez les [concepts](luis-concept-keys.md) relatifs aux clés de création et de runtime.
* Découvrez [comment affecter des clés](luis-how-to-azure-subscription.md) et ajouter des [contributeurs](luis-how-to-collaborate.md).
