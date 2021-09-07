---
title: Guide pratique pour analyser Azure Data Explorer
description: Ce guide pratique explique en détail comment analyser Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: adf9f3da66908dc473388f9b3da14439de86ddcb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562866"
---
# <a name="register-and-scan-azure-data-explorer"></a>Inscription et analyse d’Azure Data Explorer

Cet article explique comment inscrire un compte Azure Data Explorer dans Azure Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Azure Data Explorer prend en charge les analyses complètes et incrémentielles pour capturer les métadonnées et le schéma. Ces analyses permettent également de classer les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Il n’existe qu’une seule façon de configurer l’authentification pour Azure Data Explorer :

- Principal de service

### <a name="service-principal"></a>Principal du service

Si vous souhaitez appliquer l’authentification par principal de service pour les analyses, vous pouvez utiliser un principal de service existant ou en créer un. 

> [!Note]
> Si vous devez créer un principal de service, procédez comme suit :
> 1. Accédez au [portail Azure](https://portal.azure.com).
> 1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
> 1. Sélectionnez **Inscriptions d’applications**.
> 1. Sélectionnez **+ Nouvelle inscription d’application**.
> 1. Entrez un nom pour l’**application** (nom du principal de service).
> 1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
> 1. Pour l’URI de redirection, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ni qu’elle fonctionne.
> 1. Sélectionnez ensuite **Inscription**.

Il est nécessaire de récupérer l’ID d’application et le secret du principal de service :

1. Accédez à votre principal de service sur le [Portail Azure](https://portal.azure.com).
1. Copiez les valeurs **ID d’application (client)** dans **Vue d’ensemble** et **Secret client** dans **Certificats et secrets**.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** de votre choix et la **valeur** comme **secret client** de votre principal de service.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse.

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Octroi au principal du service de l’accès à l’instance Azure Data Explorer

1. Accédez au portail Azure. Ensuite, accédez à votre instance Azure Data Explorer.

1. Ajoutez le principal du service au rôle **AllDatabasesViewer** dans l’onglet **Autorisations** (cf. capture d’écran suivante).

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Capture d’écran montrant comment ajouter un principal de service dans les autorisations" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Inscription d’un compte Azure Data Explorer

Pour inscrire un nouveau compte Azure Data Explorer (Kusto) dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Data Explorer**.
1. Sélectionnez **Continue** (Continuer)

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Sur l’écran **Inscrire des sources (Azure Data Explorer (Kusto))** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer Azure Data Explorer.
3. Sélectionnez un cluster approprié.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source Azure Data Explorer que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données. 

   :::image type="content" source="media/register-scan-azure-data-explorer/set-up-scan-data-explorer.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des bases de données spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-data-explorer/scope-your-scan-data-explorer.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-data-explorer/scan-rule-set-data-explorer.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-data-explorer/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
