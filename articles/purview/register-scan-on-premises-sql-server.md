---
title: Inscrire et analyser un serveur SQL local
description: Ce tutoriel explique comment analyser un serveur SQL local à l’aide d’un runtime d’intégration auto-hébergé dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4cef99adecadc73f105dfffcdc72163c8b622cc3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208641"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Inscrire et analyser un serveur SQL local

Cet article explique comment inscrire une source de données de serveur SQL dans Purview et configurer une analyse sur celle-ci.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source de données locale de serveur SQL prend en charge les fonctionnalités suivantes :

- **Analyses complètes et incrémentielles** pour capturer des métadonnées et une classification dans un réseau local ou un serveur SQL installé sur une machine virtuelle Azure.

- **Traçabilité** entre les ressources de données pour les activités de copie/dataflow d’ADF.

La source de données locale du serveur SQL prend en charge :

- toutes les versions, de SQL Server 2019 à SQL Server 2000 ;

- méthode d’authentification Authentification SQL

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).

- Configurez un [runtime d’intégration auto-hébergé](manage-integration-runtimes.md) pour analyser la source de données.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Il n’existe qu’une seule façon de configurer l’authentification pour un serveur SQL local :

- Authentification SQL

### <a name="sql-authentication"></a>Authentification SQL

Le compte SQL doit avoir accès à la base de données **MASTER**. Cela est dû au fait que `sys.databases` se trouve dans la base de données MASTER. Le scanneur Purview doit énumérer `sys.databases` pour rechercher toutes les bases de données SQL sur le serveur.

#### <a name="creating-a-new-login-and-user"></a>Création d’une connexion et d’un utilisateur

Si vous souhaitez créer une connexion et un utilisateur pour pouvoir analyser votre serveur SQL, procédez comme suit :

> [!Note]
   > Toutes les étapes ci-dessous peuvent être exécutées à l’aide du code fourni [ici](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Accédez à SQL Server Management Studio (SSMS), connectez-vous au serveur, accédez à la sécurité, sélectionnez et maintenez l’appui (ou cliquez avec le bouton droit) sur la connexion et créez une connexion. Veillez à sélectionner l’authentification SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Créer une connexion et un utilisateur.":::

2. Sélectionnez Rôles serveur dans le volet de navigation gauche et vérifiez que le rôle public est attribué.

3. Sélectionnez Mappage d’utilisateur dans le volet de navigation de gauche, sélectionnez toutes les bases de données dans le mappage et sélectionnez le rôle de base de données : **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mappage d’utilisateur.":::

4. Sélectionnez OK pour enregistrer.

5. Accédez de nouveau à l’utilisateur que vous avez créé, en sélectionnant et en maintenant l’appui (ou en cliquant avec le bouton droit) et en sélectionnant **Propriétés**. Entrez un nouveau mot de passe et confirmez-le. Sélectionnez « Spécifier l’ancien mot de passe », puis entrez l’ancien mot de passe. **Vous devez modifier votre mot de passe dès que vous créez une connexion.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="modifier un mot de passe.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Stockage de votre mot de passe de connexion SQL dans un coffre de clés et création d’informations d’identification dans Purview

1. Accédez à votre coffre de clés dans le portail Azure1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer**, puis entrez le **Nom** et la **Valeur** en tant que *mot de passe* de votre connexion au serveur SQL.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez de nouvelles informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du **nom d’utilisateur** et du **mot de passe** pour configurer votre analyse.

## <a name="register-a-sql-server-data-source"></a>Inscrire une source de données de serveur SQL

1. Accédez à votre compte Purview.

1. Sous sources et analyse dans le volet de navigation de gauche, sélectionnez les **runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](manage-integration-runtimes.md) afin de créer un runtime d’intégration auto-hébergé pour les analyses sur une machine virtuelle locale ou Azure qui a accès à votre réseau local.

1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Sélectionnez **SQL Server**, puis **Continuer**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configurer la source de données SQL.":::

5. Fournissez un nom convivial et un point de terminaison de serveur, puis sélectionnez **Terminer** pour inscrire la source de données. Si, par exemple, votre nom de domaine complet SQL Server est **foobar.database.windows.net**, entrez *foobar* en tant que point de terminaison de serveur.

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source de serveur SQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
