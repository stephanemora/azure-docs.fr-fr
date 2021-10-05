---
title: Comment enregistrer et analyser les fichiers Azure
description: Ce guide pratique décrit en détail comment analyser les fichiers Azure dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: 2db2b5343b8a55e29881bf0908fded0a48b90b78
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209905"
---
# <a name="register-and-scan-azure-files"></a>Inscription et analyse de fichiers Azure Files

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Azure Files prend en charge les analyses complètes et incrémentielles pour capturer les métadonnées et les classifications, basées sur un système par défaut et des règles de classification personnalisées.

Pour les types de fichiers comme CSV, TSV, PSV et SSV, le schéma est extrait quand les logiques suivantes sont en place :

1. Les valeurs de la première ligne ne sont pas vides
2. Les valeurs de la première ligne sont uniques
3. Les valeurs de la première ligne ne sont ni une date ni un nombre

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données pour pouvoir configurer et planifier des analyses. Pour plus d’informations, consultez [Autorisations du catalogue](catalog-permissions.md).

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

Actuellement, il n’existe qu’une seule façon de configurer l’authentification pour le partage de fichiers Azure :

- Clé du compte

### <a name="account-key"></a>Clé du compte

Lorsque la méthode d’authentification sélectionnée est **Clé de compte**, vous devez récupérer votre clé d’accès et la stocker dans le coffre de clés :

1. Accédez à votre compte de stockage
1. Sélectionnez **Paramètres > Clés d’accès**
1. Copiez votre *clé* et enregistrez-la quelque part pour les étapes suivantes
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer**, puis entrez le **Nom** et la **Valeur** comme *clé* de votre compte de stockage.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

## <a name="register-an-azure-files-storage-account"></a>Inscription d’un compte de stockage Azure Files

Pour inscrire un nouveau compte Azure Files dans votre catalogue de données, suivez ces étapes :

1. Accédez à votre Purview Data Studio.
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Files**.
1. Sélectionnez **Continue** (Continuer)

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Sur l’écran **Inscrire des sources (Azure Files)** , suivez ces étapes :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer les comptes Stockage Azure.
3. Sélectionnez un compte Stockage Azure.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer et exécuter une nouvelle analyse, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Azure Files que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification de la clé de compte pour vous connecter à votre source de données. 

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des bases de données spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification pour qu’elle se reproduise ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.


[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
