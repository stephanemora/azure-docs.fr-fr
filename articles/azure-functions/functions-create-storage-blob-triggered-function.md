---
title: Créer une fonction dans Azure déclenchée par le stockage Blob
description: Utilisez Azure Functions pour créer une fonction sans serveur appelée par les éléments ajoutés au stockage Blob Azure.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: quickstart
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 65717e4373f64ae38a324fd19624f049dba9dfb1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769300"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Créer une fonction déclenchée par un stockage Blob Azure

Apprenez à créer une fonction déclenchée lorsque des fichiers sont chargés dans ou mis à jour dans le stockage Blob Azure.

![Affichez le message dans les journaux d’activité.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Conditions préalables requises

+ Télécharger et installer l’[Explorateur de Stockage Microsoft Azure](https://storageexplorer.com/).
+ Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Créer une fonction déclenchée par le stockage Blob

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre application de fonction, sélectionnez **Dans le portail**, puis **Continuer**. Sinon, passez à l’étape 3.

   ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. Choisissez **Autres modèles**, puis **Terminer et afficher les modèles**.

    ![Page de démarrage rapide Functions permettant de choisir d’autres modèles](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. Dans le champ Rechercher, tapez `blob`, puis choisissez le modèle **Déclencheur d’objet blob**.

1. Si vous y êtes invité, sélectionnez **Installer** pour installer l’extension Stockage Azure et toutes les dépendances dans l’application de fonction. Une fois l’installation réussie, sélectionnez **Continuer**.

    ![Installer des extensions de liaison](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. Utilisez les paramètres spécifiés dans le tableau sous l’image.

    ![Création de la fonction déclenchée par le stockage Blob.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom** | Unique dans votre Function App | Nom de cette fonction déclenchée par l’objet Blob. |
    | **Chemin d’accès**   | samples-workitems/{name}    | Emplacement du stockage Blob analysé. Le nom de fichier de l’objet Blob est transmis dans la liaison en tant que paramètre _name_.  |
    | **Connexion au compte de stockage** | AzureWebJobsStorage | Vous pouvez utiliser la connexion de compte de stockage qui est déjà utilisée par votre application de fonction, ou créez-en une.  |

1. Cliquez sur **Créer** pour créer votre fonction.

Ensuite, vous vous connectez à votre compte Stockage Azure et créez le conteneur **samples-workitems**.

## <a name="create-the-container"></a>Créer le conteneur

1. Dans votre fonction, cliquez sur **Intégrer**, développez **Documentation** et copiez le **Nom du compte** et la **Clé du compte**. Vous utilisez ces informations d’identification pour vous connecter au compte de stockage. Si vous avez déjà connecté votre compte de stockage, passez à l’étape 4.

    ![Obtenez les informations d’identification de connexion au compte de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Exécutez [l’Explorateur de stockage Microsoft Azure](https://storageexplorer.com/), cliquez sur l’icône de connexion située sur la gauche, choisissez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**.

    ![Exécutez l’outil Explorateur de compte de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Saisissez le **Nom du compte** et la **Clé du compte** récupérés à l’étape 1, puis cliquez sur **Suivant** et sur **Connexion**. 

    ![Entrez les informations d’identification de stockage et connectez-vous.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Développez le compte de stockage attaché, cliquez avec le bouton droit sur **Conteneurs d’objets blob**, cliquez sur **Créer un conteneur d’objets blob**, tapez `samples-workitems` et appuyez sur Entrée.

    ![Créez une file d’attente de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Une fois que vous avez un conteneur d’objets blob, vous pouvez tester la fonction en chargeant un fichier dans le conteneur.

## <a name="test-the-function"></a>Tester la fonction

1. Dans le portail Azure, accédez à votre fonction, développez les **Journaux d’activité** en bas de la page et vérifiez que la diffusion de journaux d’activité n’est pas suspendue.

1. Dans l’Explorateur Stockage, développez votre compte de stockage, **Conteneurs d’objets blob** et **samples-workitems**. Cliquez sur **Charger**, puis sur **Charger des fichiers...** .

    ![Chargement d’un fichier dans le conteneur d’objets blob.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Dans la boîte de dialogue **Charger des fichiers**, cliquez sur le champ **Fichiers**. Accédez à un fichier sur votre ordinateur local, par exemple un fichier image, sélectionnez-le, puis cliquez sur **Ouvrir** et sur **Charger**.

1. Revenez à vos journaux d’activité de fonction et vérifiez que l’objet blob a été lu.

   ![Affichez le message dans les journaux d’activité.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Lorsque votre Function App s’exécute dans le plan de consommation par défaut, il peut s’écouler un délai de plusieurs minutes entre l’ajout ou la mise à jour de l’objet blob et le déclenchement de la fonction. Si vous exigez une faible latence pour vos fonctions déclenchées par des objets Blob, exécutez plutôt votre Function App dans un plan App Service.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’un objet blob est ajouté ou mis à jour dans le stockage Blob. Pour plus d’informations sur les déclencheurs de stockage Blob, consultez [Liaisons de stockage Blob Azure Functions](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
