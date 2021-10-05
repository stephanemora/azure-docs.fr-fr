---
title: 'Tutoriel : Inscrire et analyser un serveur SQL Server local'
description: Ce tutoriel explique comment inscrire un serveur SQL Server local dans Purview et analyser ce serveur à l’aide d’un runtime d’intégration auto-hébergé.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0054b41fdf12efa4bbd3f1bf34e66023b7ea2d75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213145"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>Tutoriel : Inscrire et analyser un serveur SQL Server local

Azure Purview est conçu pour se connecter aux sources de données afin de vous aider à gérer les données sensibles, à simplifier la découverte des données et à garantir une utilisation appropriée. Purview peut se connecter à des sources dans l’ensemble de votre paysage, y compris dans des structures multiclouds et locales. Pour ce scénario, vous allez utiliser un runtime d’intégration auto-hébergé pour vous connecter aux données sur un serveur SQL local. Vous utiliserez ensuite Azure Purview pour analyser et classer ces données.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se connecter à Purview Studio.
> * Créer une collection dans Azure Purview.
> * Créez un runtime d’intégration auto-hébergé.
> * Stocker les informations d’identification dans un coffre Azure Key Vault.
> * Inscrire un serveur SQL Server local dans Purview.
> * Analyser le serveur SQL Server.
> * Parcourir votre catalogue de données pour afficher les ressources sur votre serveur SQL Server.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un [coffre de clés Azure](../key-vault/certificates/quick-create-portal.md#create-a-vault) actif.
- Un compte Azure Purview. Si vous n’en avez pas déjà un, vous pouvez [suivre notre guide de démarrage rapide pour en créer un](create-catalog-portal.md).
- Un [serveur SQL Server local](https://www.microsoft.com/sql-server/sql-server-downloads).

## <a name="sign-in-to-purview-studio"></a>Se connecter à Purview Studio

Pour interagir avec Purview, vous allez vous connecter à [Purview Studio](https://web.purview.azure.com/resource/) par le biais du portail Azure. Pour ouvrir le studio, accédez à votre ressource Purview sur le [Portail Azure](https://portal.azure.com), puis sélectionnez la vignette **Ouvrir Purview Studio** sur la page de présentation.

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Capture d’écran de la fenêtre Purview dans le portail Azure, avec le bouton Purview Studio encadré." border="true":::

## <a name="create-a-collection"></a>Création d'une collection

Les collections dans Azure Purview sont utilisées pour organiser les ressources et les sources dans une hiérarchie personnalisée pour l’organisation et la détectabilité. Elles constituent également l’outil utilisé pour gérer l’accès dans Purview. Dans ce tutoriel, nous allons créer une collection pour héberger votre source SQL Server et toutes ses ressources. Ce tutoriel ne couvre pas les informations relatives à l’attribution d’autorisations à d’autres utilisateurs. Ainsi, pour plus d’informations, vous pouvez suivre notre [Guide des autorisations Purview](catalog-permissions.md).

### <a name="check-permissions"></a>Vérifiez les autorisations

Pour créer et gérer des collections dans Purview, vous devez être **administrateur de collection** dans Purview. Vous pouvez vérifier ces autorisations dans [Purview Studio](use-purview-studio.md).

1. Sélectionnez **Data Map > Collections** dans le volet gauche pour ouvrir la page de gestion des collections.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec l’onglet Collections sélectionné." border="true":::

1. Sélectionnez votre collection racine. La collection racine est la première collection de votre liste de collections et elle portera le même nom que votre ressource Purview. Dans notre exemple ci-dessous, elle s’appelle Compte Purview.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec la collection racine mise en évidence." border="true":::

1. Sélectionnez l’onglet **Attributions de rôles** dans la fenêtre collection.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec l’onglet d’attributions de rôle racine mise en évidence." border="true":::

1. Pour créer une collection, vous devez figurer dans la liste des administrateurs de collections sous les attributions de rôles. En principe, si vous êtes l’auteur de la ressource Purview, vous figurez déjà parmi les administrateurs de collection sous la collection racine. Si ce n’est pas le cas, vous devez contacter l’administrateur de collection pour qu’il vous accorde l’autorisation.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec la section Administrateurs de collection encadrée." border="true":::

### <a name="create-the-collection"></a>Créer la collection

1. Sélectionnez **+ Ajouter une collection**. Là encore, seuls les [administrateurs de collection](#check-permissions) peuvent gérer des collections.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection avec les boutons d’ajout de collection encadrés" border="true":::

1. Dans le volet droit, entrez le nom et la description de la collection. Si nécessaire, vous pouvez également ajouter des utilisateurs ou des groupes en tant qu’administrateurs de collections à la nouvelle collection.
1. Sélectionnez **Créer**.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection, avec un nom d’affichage et les administrateurs de collections sélectionnés, ainsi que le bouton créer mis en évidence." border="true":::

1. Les informations de la nouvelle collection seront reflétées dans la page.

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la collection nouvellement créée." border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé

Le runtime d’intégration auto-hébergé (SHIR) est l’infrastructure de calcul utilisée par Purview pour se connecter aux sources de données locales. Le SHIR est téléchargé et installé sur un ordinateur situé dans le même réseau que la source de données locale.

Ce tutoriel part du principe que l’ordinateur sur lequel vous allez installer votre runtime d’intégration auto-hébergé peut établir des connexions réseau à Internet. Cette connexion permet au SHIR de communiquer entre votre source et Azure Purview. Si votre ordinateur dispose d’un pare-feu restreint ou si vous souhaitez sécuriser votre pare-feu, consultez la [configuration requise du réseau pour le runtime d’intégration auto-hébergé](manage-integration-runtimes.md#networking-requirements).

1. Dans la page d’accueil de Purview Studio, sélectionnez **Data Map** dans le volet de navigation gauche.

1. Sous **Gestion source** dans le volet gauche, sélectionnez **Runtimes d’intégration**, puis **+ Nouveau**.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Sélectionner le bouton Runtimes d’intégration":::

1. Sur la page **Configuration des runtimes d’intégration**, sélectionnez **Auto-hébergé** pour créer un runtime d’intégration auto-hébergé, puis **Continuer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="Création d’un runtime d’intégration auto-hébergé":::

1. Entrez un nom pour votre runtime d’intégration, puis sélectionnez Créer.

1. Sur la page **Paramètres des runtime d’intégration**, suivez la procédure décrite dans la section **Configuration manuelle**. Vous devrez télécharger le runtime d’intégration à partir du site de téléchargement sur une machine virtuelle ou un ordinateur qui se trouve dans le même réseau que votre serveur SQL Server local. Pour plus d’informations sur le type d’ordinateur requis, vous pouvez suivre notre [Guide pour gérer les runtimes d’intégration](manage-integration-runtimes.md#prerequisites).

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="Récupération de la clé":::

   - Copiez et collez la clé d’authentification.

   - Téléchargez le runtime intégration auto-hébergé sur une machine Windows locale à partir de [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717). Exécutez le programme d’installation. Les versions du runtime d’intégration auto-hébergées telles que 5.4.7803.1 et 5.6.7795.1 sont prises en charge. 

   - Dans la page **Inscrire le runtime d’intégration (auto-hébergé)** , collez une des deux clés que vous avez enregistrées avant, puis sélectionnez **Inscrire**.

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="Clé d’entrée":::

   - Dans la page **Nouveau runtime d’intégration (auto-hébergé)** , sélectionnez **Terminer**.

1. Une fois le runtime d’intégration auto-hébergé correctement inscrit, vous voyez la fenêtre suivante :

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="Inscription réussie":::

## <a name="set-up-sql-authentication"></a>Configurer l’authentification SQL

Il n’existe qu’une seule façon de configurer l’authentification pour un serveur SQL local :

- Authentification SQL

### <a name="sql-authentication"></a>Authentification SQL

Le compte SQL doit avoir accès à la base de données **MASTER**. Cela est dû au fait que `sys.databases` se trouve dans la base de données. Le scanneur Purview doit énumérer `sys.databases` pour rechercher toutes les bases de données SQL sur le serveur.

#### <a name="create-a-new-login-and-user"></a>Créer une connexion et un utilisateur

Si vous souhaitez créer une connexion et un utilisateur pour pouvoir analyser votre serveur SQL, procédez comme suit :

> [!Note]
> Toutes les étapes ci-dessous peuvent être exécutées à l’aide du code fourni [ici](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Accédez à SQL Server Management Studio (SSMS), connectez-vous au serveur, accédez à la sécurité, sélectionnez et maintenez l’appui (ou cliquez avec le bouton droit) sur la connexion et créez une connexion. Veillez à sélectionner l’authentification SQL.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Créer une connexion et un utilisateur.":::

1. Sélectionnez Rôles serveur dans le volet de navigation gauche et vérifiez que le rôle public est attribué.

1. Sélectionnez Mappage d’utilisateur dans le volet de navigation de gauche, sélectionnez toutes les bases de données dans le mappage et sélectionnez le rôle de base de données : **db_datareader**.

1. Sélectionnez **OK** pour enregistrer.

1. Accédez de nouveau à l’utilisateur que vous avez créé, en sélectionnant et en maintenant l’appui (ou en cliquant avec le bouton droit) sur l’utilisateur et en sélectionnant **Propriétés**. Entrez un nouveau mot de passe et confirmez-le. Sélectionnez « Spécifier l’ancien mot de passe », puis entrez l’ancien mot de passe. **Vous devez modifier votre mot de passe dès que vous créez une connexion.**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="modifier un mot de passe.":::

#### <a name="create-a-key-vault-credential"></a>Créer des informations d’identification Key Vault

1. Accédez à votre coffre de clés dans le portail Azure. Sélectionnez **Paramètres > Secrets**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Sélectionner Secrets dans le menu gauche":::

1. Sélectionnez **+ Générer/importer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="Sélectionner Générer/importer dans le menu supérieur.":::

1. Pour les options de chargement, sélectionnez **+ Manuel** et entrez le **Nom** et la **Valeur** en tant que *mot de passe* de votre connexion au serveur SQL. Vérifiez que le paramètre **Activé** est défini sur **Oui**. Si vous définissez une date d’activation et d’expiration, veillez à ce que la date du jour soit comprise entre les deux, sans quoi vous ne pourrez pas utiliser les informations d’identification.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="Ajouter des valeurs aux informations d’identification du coffre de clés":::

1. Sélectionnez **Créer** pour terminer.
1. Dans [Azure Purview Studio](#sign-in-to-purview-studio), accédez à la page **Gestion** dans le menu de gauche.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="Sélectionner la page Gestion dans le menu de gauche":::

1. Sélectionnez la page **Informations d’identification**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="Le bouton Informations d’identification est encadré dans la page de gestion.":::

1. Dans la page **Informations d’identification**, sélectionnez **Gérer les connexions Key Vault**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="Gérer les connexions Azure Key Vault":::

1. Sélectionnez **+Nouveau** dans la page Gérer les connexions Key Vault.

1. Renseignez les informations demandées, puis sélectionnez **Créer**.

1. Vérifiez que votre coffre de clés Key Vault a bien été associé à votre compte Azure Purview, en procédant de la façon suivante :

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="Afficher les connexions Azure Key Vault à des fins de vérification":::

1. Créez de nouvelles informations d’identification pour SQL Server en sélectionnant **+ Nouveau**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="Sélectionner + Nouveau pour créer des informations d’identification":::

1. Fournissez les informations nécessaires. Sélectionnez la **Méthode d’authentification**, ainsi qu’une **Connexion Key Vault** à partir de laquelle sélectionner un secret.

1. Une fois tous les détails renseignés, sélectionnez **Créer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="Nouvelles informations d'identification":::

1. Vérifiez que vos nouvelles informations d’identification figurent dans la liste et qu’elles sont prêtes à être utilisées.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="Afficher les informations d'identification":::

## <a name="register-sql-server"></a>Inscrire SQL Server

1. Accédez à votre compte Purview dans le [portail Azure](https://portal.azure.com) et sélectionnez [Purview Studio](#sign-in-to-purview-studio).

1. Sous sources et analyse dans le volet de navigation de gauche, sélectionnez les **runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](manage-integration-runtimes.md) afin de créer un runtime d’intégration auto-hébergé pour les analyses sur une machine virtuelle locale ou Azure ayant accès à votre réseau local.

1. Sélectionnez **Data Map** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Sélectionnez **SQL Server**, puis **Continuer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configurer la source de données SQL.":::

1. Fournissez un nom convivial et un point de terminaison de serveur, puis sélectionnez **Terminer** pour inscrire la source de données. Si, par exemple, votre nom de domaine complet SQL Server est **foobar.database.windows.net**, entrez *foobar* en tant que point de terminaison de serveur.

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source de serveur SQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette source Purview ou SQL, vous pouvez suivre les étapes ci-dessous pour supprimer le runtime d’intégration, les informations d’identification SQL et les ressources Purview.

### <a name="remove-shir-from-purview"></a>Supprimer le SHIR de Purview

1. Dans la page d’accueil de [Purview Studio](https://web.purview.azure.com/resource/), sélectionnez **Data Map** dans le volet de navigation gauche.

1. Sous **Gestion source** dans le volet gauche, sélectionnez **Runtimes d’intégration**.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="Sélectionner le bouton Runtimes d’intégration":::

1. Cochez la case en regard de votre runtime d’intégration, puis sélectionnez le bouton **Supprimer**.

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="Case à cocher en regard du runtime d’intégration et bouton Supprimer encadré":::

1. Sélectionnez **Confirmer** dans la fenêtre suivante pour confirmer la suppression.

1. La fenêtre s’actualise automatiquement et vous ne devriez plus voir votre SHIR listé sous Runtimes d’intégration.

### <a name="uninstall-self-hosted-integration-runtime"></a>Désinstaller le runtime d’intégration auto-hébergé

1. Connectez-vous à l’ordinateur sur lequel votre runtime d’intégration auto-hébergé est installé.
1. Ouvrez le panneau de configuration puis, sous *Désinstaller un programme*, recherchez « Microsoft Integration Runtime ».

1. Désinstallez le runtime d’intégration existant.

> [!IMPORTANT] 
> Dans le processus suivant, sélectionnez Oui. Ne conservez pas les données pendant le processus de désinstallation.

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="Capture d’écran du bouton « Oui » pour la suppression de toutes les données utilisateur du runtime d’intégration.":::

### <a name="remove-sql-credentials"></a>Supprimer des informations d’identification SQL

1. Accédez au [portail Azure](https://portal.azure.com) et accédez à la ressource Key Vault dans laquelle vous avez stocké vos informations d’identification Purview.

1. Sous **Paramètres** dans le menu de gauche, sélectionnez **Secrets**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Sélectionner Secrets dans le menu de gauche dans Azure Key Vault":::

1. Sélectionnez le secret des informations d’identification SQL Server que vous avez créé pour ce tutoriel.
1. Sélectionnez **Supprimer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="Supprimer le secret du menu supérieur dans Secret Azure Key Vault":::

1. Sélectionnez **Oui** pour supprimer définitivement la ressource.

### <a name="delete-purview-account"></a>Supprimer un compte Purview

Si vous souhaitez supprimer votre compte Purview à la fin de ce tutoriel, procédez comme suit.

1. Accédez au [portail Azure](https://portal.azure.com), puis à votre compte Purview.

1. En haut de la page, sélectionnez le bouton **Supprimer**.

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="Le bouton Supprimer de la page Compte Purview dans le portail Azure est sélectionné.":::

1. Une fois le processus terminé, vous recevez une notification dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser les API REST Purview](tutorial-using-rest-apis.md)
