---
title: Tutoriel - Accéder au Stockage Blob à l’aide du coffre de clés et d’Azure Databricks
description: Ce tutoriel explique comment accéder à Stockage Blob Azure à partir d’Azure Databricks en utilisant des secrets stockés dans un coffre de clés.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: f1472d7043a092f35e10fdedbf4c52740e87c51c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706139"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Didacticiel : Accéder à Stockage Blob Azure à partir d’Azure Databricks en utilisant Azure Key Vault

Ce tutoriel explique comment accéder à Stockage Blob Azure à partir d’Azure Databricks en utilisant des secrets stockés dans un coffre de clés.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte de stockage et un conteneur d’objets blob
> * Créer un coffre Azure Key Vault et ajouter un secret
> * Créer un espace de travail Azure Databricks et une étendue de secrets
> * Accéder à votre conteneur d’objets blob à partir d’Azure Databricks

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

> [!Note]
> Ce didacticiel ne peut pas être suivi avec un **abonnement d’essai gratuit Azure**.
> Si vous avez un compte gratuit, accédez à votre profil et modifiez votre abonnement sur **Paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/). Ensuite, [supprimez la limite de dépense](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), et [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour les processeurs virtuels dans votre région. Lorsque vous créez votre espace de travail Azure Databricks, vous pouvez sélectionner le tarif **Version d’évaluation (Premium - 14 jours de DBU offerts)** pour donner à l’accès de l’espace de travail un accès gratuit aux DBU d’Azure Databricks pendant 14 jours.

## <a name="create-a-storage-account-and-blob-container"></a>Créer un compte de stockage et un conteneur d’objets blob

1. Dans le Portail Azure, sélectionnez **Créer une ressource** > **Stockage**. Ensuite, sélectionnez **Compte de stockage**.

   ![Rechercher une ressource de compte de stockage Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Sélectionnez votre abonnement et votre groupe de ressources, ou créez un groupe de ressources. Entrez ensuite un nom de compte de stockage, puis choisissez un emplacement. Sélectionnez **Vérifier + créer**.

   ![Définir les propriétés du compte de stockage](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Si la validation échoue, résolvez les problèmes et réessayez. Si la validation réussit, sélectionnez **Créer** et attendez que le compte de stockage soit créé.

4. Accédez à votre compte de stockage nouvellement créé et sélectionnez **Objets blob**sous **Services** dans la page **Vue d’ensemble**. Sélectionnez ensuite **+ Conteneur** et entrez un nom de conteneur. Sélectionnez **OK**.

   ![Créer un conteneur](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Recherchez un fichier que vous voulez charger dans votre conteneur de stockage d’objets blob. Si vous n’avez pas de fichier, utilisez un éditeur de texte pour créer un fichier texte contenant quelques informations. Dans cet exemple, un fichier nommé **hw.txt** contient le texte « hello world ». Enregistrez votre fichier texte localement, puis chargez-le dans votre conteneur de stockage d’objets blob.

   ![Charger le fichier dans le conteneur](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Revenez à votre compte de stockage et sélectionnez **Clés d’accès** sous **Paramètres**. Copiez **Nom du compte de stockage** et **clé 1** dans un éditeur de texte pour une utilisation ultérieure dans ce tutoriel.

   ![Rechercher les clés d’accès du compte de stockage](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Créer un coffre Azure Key Vault et ajouter un secret

1. Dans le portail Azure, sélectionnez **Créer une ressource** et entrez **Key Vault** dans la barre de recherche.

   ![Zone de recherche Créer une ressource Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. La ressource Key Vault est automatiquement sélectionnée. Sélectionnez **Create** (Créer).

   ![Créez une ressource Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Dans la page **Créer un coffre de clés**, entrez les informations suivantes et conservez les valeurs par défaut pour les champs restants :

   |Propriété|Description|
   |--------|-----------|
   |Nom|Nom unique pour votre coffre de clés.|
   |Subscription|Choisissez un abonnement.|
   |Resource group|Choisissez un groupe de ressources ou créez-en un.|
   |Location|Choisissez un emplacement.|

   ![Propriétés du coffre de clés Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Après avoir renseigné les informations ci-dessus, sélectionnez **Créer**. 

4. Accédez à votre coffre de clés nouvellement créé dans le portail Azure et sélectionnez **Secrets**. Sélectionnez ensuite **Générer/importer**. 

   ![Générer un nouveau secret de coffre de clés](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Dans la page **Créer un secret**, entrez les informations suivantes et conservez les valeurs par défaut pour les champs restants :

   |Propriété|Valeur|
   |--------|-----------|
   |Options de chargement|Manuel|
   |Nom|Nom convivial de votre clé de compte de stockage.|
   |Valeur|Clé 1 de votre compte de stockage.|

   ![Propriétés du nouveau secret de coffre de clés](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Enregistrez le nom de la clé dans un éditeur de texte pour l’utiliser plus tard dans ce tutoriel, puis sélectionnez **Créer**. Ensuite, accédez au menu **Propriétés**. Copiez le **Nom DNS** et l’**ID de ressource** dans un éditeur de texte pour une utilisation ultérieure dans le tutoriel.

   ![Copier le nom DNS et l’ID de ressource du coffre de clés Azure Key Vault](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Créer un espace de travail Azure Databricks et une étendue de secrets

1. Dans le portail Azure, sélectionnez **Créer une ressource** >  **Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Sous **Service Azure Databricks**, renseignez les valeurs suivantes pour créer un espace de travail Databricks.

   |Propriété  |Description  |
   |---------|---------|
   |Nom de l’espace de travail     | Renseignez un nom pour votre espace de travail Databricks.        |
   |Subscription     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
   |Resource group     | Sélectionnez le même groupe de ressources que celui qui contient votre coffre de clés. |
   |Location     | Sélectionnez le même emplacement que votre coffre de clés Azure. Pour connaître toutes les régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).        |
   |Niveau de tarification     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Propriétés de l’espace de travail Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Sélectionnez **Create** (Créer).

3. Accédez à votre ressource Azure Databricks nouvellement créée dans le portail Azure et sélectionnez **Lancer l’espace de travail**.

   ![Lancer l’espace de travail Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Une fois que votre espace de travail Azure Databricks est ouvert dans une fenêtre distincte, ajoutez **#secrets/createScope** à l’URL. L’URL doit avoir le format suivant : 

   **https://<\location>.azuredatabricks.net/#secrets/createScope**.

5. Entrez un nom d’étendue, puis entrez le nom DNS et l’ID de ressource du coffre de clés Azure que vous avez enregistrés précédemment. Enregistrez le nom de l’étendue dans un éditeur de texte pour l’utiliser plus tard dans ce tutoriel. Sélectionnez ensuite **Create** (Créer).

   ![Créer une étendue de secrets dans l’espace de travail Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Accéder à votre conteneur d’objets blob à partir d’Azure Databricks

1. Dans la page d’accueil de votre espace de travail Azure Databricks, sélectionnez **Nouveau cluster** sous **Tâches courantes**.

   ![Créer un notebook Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Entrez un nom de cluster et sélectionnez **Créer un cluster**. La création du compte prend quelques minutes.

3. Une fois le cluster créé, accédez à la page d’accueil de votre espace de travail Azure Databricks, sélectionnez **Nouveau notebook** sous **Tâches courantes**.

   ![Créer un notebook Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Entrez un nom de notebook et définissez le langage sur Python. Définissez le cluster sur le nom du cluster que vous avez créé à l’étape précédente.

5. Exécutez la commande suivante pour monter votre conteneur de stockage d’objets blob. Rappelez-vous de changer les valeurs pour les propriétés suivantes :

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** est un chemin DBFS qui représente l’emplacement où le conteneur de stockage d’objets blob ou un dossier à l’intérieur du conteneur (spécifié dans la source) sera monté.
   * **conf-key** peut être `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` ou `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** est le nom de l’étendue de secrets que vous avez créée dans la section précédente. 
   * **key-name** est le nom du secret que vous avez créé pour la clé du compte de stockage dans votre coffre de clés.

   ![Créer un montage de stockage d’objets blob dans le notebook](./media/store-secrets-azure-key-vault/command1.png)

6. Exécutez la commande suivante pour lire le fichier texte de votre conteneur de stockage d’objets blob vers un dataframe. Changez les valeurs de la commande pour qu’elles correspondent au nom de votre montage et de votre fichier.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Lire le fichier vers un dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. Utilisez la commande suivante pour afficher le contenu de votre fichier.

   ```python
   df.show()
   ```
   ![Afficher le dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Pour démonter votre stockage d’objets blob, exécutez la commande suivante :

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Démonter le compte de stockage](./media/store-secrets-azure-key-vault/command4.png)

9. Notez qu’une fois que le montage a été démonté, vous ne pouvez plus lire à partir de votre compte de stockage d’objets blob.

   ![Erreur de démontage du compte de stockage](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez tout votre groupe de ressources en effectuant les étapes suivantes :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis accédez à votre groupe de ressources.

2. Sélectionnez **Supprimer le groupe de ressources**, puis tapez le nom de votre groupe de ressources. Puis sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment implémenter un environnement Databricks injecté par réseau virtuel avec un point de terminaison de service activé pour Cosmos DB.
> [!div class="nextstepaction"]
> [Tutoriel : Implémenter Azure Databricks avec un point de terminaison Cosmos DB](service-endpoint-cosmosdb.md)
