---
title: Inscrire et analyser Azure Data Lake Storage (ADLS) Gen1
description: Ce tutoriel explique comment analyser des données d’Azure Data Lake Storage Gen1 dans Azure Purview.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 1c4801814e77efdb681f32ea35d4dfb68618900b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532017"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Inscrire et analyser Azure Data Lake Storage Gen1

Cet article explique comment inscrire Azure Data Lake Storage Gen1 comme source de données dans Azure Purview et comment configurer une analyse.

> [!Note]
> Azure Data Lake Storage Gen2 est maintenant en disponibilité générale. Nous vous recommandons de commencer à l’utiliser dès aujourd'hui. Pour plus d’informations, consultez la [page du produit](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source de données Azure Data Lake Storage Gen1 prend en charge les fonctionnalités suivantes :

- **Analyses complètes et incrémentielles** pour capturer les métadonnées et la classification dans Azure Data Lake Storage Gen1

- **Traçabilité** entre les ressources de données pour les activités de copie/flux de données ADF

Pour les types de fichiers comme CSV, TSV, PSV et SSV, le schéma est extrait quand les logiques suivantes sont en place :

1. Les valeurs de la première ligne ne sont pas vides
2. Les valeurs de la première ligne sont uniques
3. Les valeurs de la première ligne ne sont ni une date ni un nombre

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Les méthodes d’authentification suivantes sont prises en charge pour Azure Data Lake Storage Gen1 :

- Identité managée
- Principal du service

### <a name="managed-identity-recommended"></a>Identité managée (recommandé)

À des fins de facilité et de sécurité, vous souhaiterez peut-être utiliser Purview MSI pour vous authentifier auprès de votre magasin de données.

Pour configurer une analyse le MSI de Data Catalog, vous devez d’abord accorder à votre compte Purview l’autorisation d’analyser la source de données. Vous devez effectuer cette étape *avant* de configurer votre analyse, sinon votre celle-ci échouera.

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>Ajout du MSI de Purview à un compte Azure Data Lake Storage Gen1

Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note]
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter une identité managée sur une ressource Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, un compte de stockage Azure Data Lake Storage Gen1) que le catalogue doit être autorisé à analyser.

2. Cliquez sur **Vue d’ensemble** et sélectionnez **Explorateur de données**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Choisir le contrôle d’accès":::

3. Cliquez sur **Accès** dans la barre de navigation supérieure

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Cliquez sur Accès":::

4. Cliquez sur **Ajouter**. Ajoutez **Purview Catalog** dans Sélectionner un utilisateur ou un groupe. Sélectionnez les autorisations **Lire** et **Exécuter**. Veillez à choisir **Ce dossier et tous ses enfants** et **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut** dans l’option Ajouter à, comme indiqué dans la capture d’écran ci-dessous. Cliquez sur **OK**
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="Détails de l’authentification du MSI":::
   
> [!Tip]
> Une **entrée d’autorisation d’accès** est une entrée d’autorisation sur les fichiers et les dossiers *actuels*.
> Une **entrée d’autorisation par défaut** est une entrée d’autorisation dont les nouveaux fichiers et dossiers *vont hériter*.
> 
> Pour accorder une autorisation seulement aux fichiers existants, **choisissez une entrée d’autorisation d’accès**.
> 
> Pour accorder une autorisation d’analyser les fichiers et dossiers qui seront ajoutés ultérieurement, **incluez une entrée d’autorisation par défaut**.
> 
> Pour plus d’informations, consultez la [documentation sur les autorisations](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une nouvelle connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse

> [!Note]
> Une fois que vous avez ajouté le MSI du catalogue sur la source de données, attendez jusqu’à 15 minutes que les autorisations soient propagées avant de configurer une analyse.

Au bout de 15 minutes environ, le catalogue doit disposer des autorisations appropriées pour pouvoir analyser la ou les ressources.

### <a name="service-principal"></a>Principal du service

Pour utiliser un principal de service, vous devez d’abord en créer un en procédant comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, sélectionnez **Azure Active Directory**.

3. Sélectionnez **Inscriptions d’applications**.

4. Sélectionnez **+ Nouvelle inscription d’application**.

5. Entrez un nom pour l’**application** (nom du principal de service).

6. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.

7. Pour **URI de redirection**, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ou qu’elle fonctionne.

8. Sélectionnez ensuite **Inscription**.

9. Copiez les valeurs à partir du nom d’affichage et de l’ID d’application.

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Ajout du principal de service Purview à un compte Azure Data Lake Storage Gen1

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, un compte de stockage Azure Data Lake Storage Gen1) que le catalogue doit être autorisé à analyser.

2. Cliquez sur **Vue d’ensemble** et sélectionnez **Explorateur de données**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Choisir le contrôle d’accès":::

3. Cliquez sur **Accès** dans la barre de navigation supérieure

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Cliquez sur Accès":::

4. Cliquez sur **Ajouter**. Ajoutez **Application du principal de service** dans Sélectionner un utilisateur ou un groupe. Sélectionnez les autorisations **Lire** et **Exécuter**. Veillez à choisir **Ce dossier et tous ses enfants** et **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut** dans l’option Ajouter à, comme indiqué dans la capture d’écran ci-dessous. Cliquez sur **OK**
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="détails de l’authentification du principal de service":::

> [!Tip]
> Une **entrée d’autorisation d’accès** est une entrée d’autorisation sur les fichiers et les dossiers *actuels*.
> Une **entrée d’autorisation par défaut** est une entrée d’autorisation dont les nouveaux fichiers et dossiers *vont hériter*.
>
> Pour accorder une autorisation seulement aux fichiers existants, **choisissez une entrée d’autorisation d’accès**.
>
> Pour accorder une autorisation d’analyser les fichiers et dossiers qui seront ajoutés ultérieurement, **incluez une entrée d’autorisation par défaut**.
>
> Pour plus d’informations, consultez la [documentation sur les autorisations](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders).

5. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une nouvelle connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Inscrire la source de données Azure Data Lake Storage Gen1

Pour inscrire un nouveau compte ADLS Gen1 dans votre catalogue de données, procédez comme suit :

1. Accédez à votre Purview Data Catalog.
2. Sélectionnez **Data Map** dans le volet de navigation de gauche.
3. Sélectionnez **Inscrire**.
4. Sous **Inscrire des sources**, sélectionnez **Azure Data Lake Storage Gen1**. 
5. Sélectionnez **Continue** (Continuer)

Dans l’écran Inscrire des sources (Azure Data Lake Storage Gen1), procédez comme suit :

1. Entrez un **nom** avec lequel la source de données sera répertoriée dans le catalogue.
2. Choisissez votre abonnement pour filtrer les comptes de stockage.
3. Sélectionnez un compte de stockage.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source Azure Data Lake Storage Gen1 que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="Configurer l’analyse":::

1. Vous pouvez limiter votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
