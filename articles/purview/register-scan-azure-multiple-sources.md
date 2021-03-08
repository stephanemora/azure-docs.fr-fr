---
title: Guide pratique pour analyser des sources multiples Azure
description: Découvrez comment analyser l’intégralité d’un abonnement Azure ou d’un groupe de ressources dans votre catalogue de données Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: c57ac9ddbebcf02cb0118705b63f97fd1880b0f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695875"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Inscrire et analyser des sources multiples Azure

Cet article explique comment inscrire une source multiple Azure (abonnements ou groupes de ressources Azure) dans Purview et comment configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La fonctionnalité de sources multiples Azure prend en charge les analyses pour capturer les métadonnées et le schéma sur la plupart des types de ressources Azure pris en charge par Purview. Il classe également les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations sur la création d’un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Vous devez être administrateur de la source de données Azure Purview.
- Configuration de l’authentification comme décrit dans les sections ci-dessous

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Configuration de l’authentification pour l’énumération des ressources dans un abonnement ou un groupe de ressources

1. Accédez à l’abonnement ou au groupe de ressources dans le portail Azure.  
1. Sélectionnez  **Access Control (IAM)**  dans le menu de navigation de gauche 
1. Vous devez être le propriétaire ou l’administrateur de l’accès utilisateur pour ajouter un rôle à l’abonnement ou au groupe de ressources. Cliquez sur le bouton *+Ajouter*. 
1. Définissez le rôle de **Lecteur** et entrez le nom de votre compte Azure Purview (il s’agit de son fichier MSI) dans la zone d’entrée Sélectionner. Cliquez sur *Enregistrer* pour terminer l’attribution de rôle.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Configuration de l’authentification pour l’analyse des ressources dans un abonnement ou un groupe de ressources

Il existe deux façons de configurer l’authentification pour une source multiple Azure :

- Identité managée
- Principal de service

> [!NOTE]
> Vous devez configurer l’authentification sur chaque ressource dans votre abonnement ou votre groupe de ressources que vous souhaitez inscrire et analyser. Les types de ressources de stockage Azure (stockage Blob Azure et Azure Data Lake Storage Gen2) facilitent cette opération en vous permettant d’ajouter le fichier MSI ou le principal du service au niveau de l’abonnement ou du groupe de ressources en tant que lecteur de données blob du stockage. Les autorisations s’étendent ensuite à chaque compte de stockage dans cet abonnement ou ce groupe de ressources. Pour tous les autres types de ressources, vous devez appliquer le fichier MSI ou le principal du service sur chaque ressource ou bien utiliser un script pour ce faire. Voici la procédure pour ajouter des autorisations sur chaque type de ressource dans un abonnement ou un groupe de ressources.
    
- [Stockage Blob Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Inscrire une source multiple Azure

Pour enregistrer une source multiple Azure dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Sources** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure (multiples)**
1. Sélectionnez **Continue** (Continuer)

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Inscrire une source multiple Azure":::

Sur l’écran **Inscrire des sources (Azure multiples)** , procédez comme suit :

1. Entrez un **Nom** sous lequel la source de données apparaîtra dans le catalogue 
1. Si vous le souhaitez, choisissez un **groupe d’administration** pour filtrer les valeurs
1. **Sélectionnez un abonnement ou un groupe de ressources spécifique** dans un abonnement donné de la liste déroulante. L’étendue d’inscription sera définie sur l’abonnement ou le groupe de ressources sélectionné  
1. Sélectionnez une **collection** ou créez-en une (facultatif)
1. **Terminez** l’inscription de la source de données

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Configurer une source multiple Azure":::

## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Accédez à la section **Sources**

1. Sélectionnez la source de données que vous avez inscrite

1. Cliquez sur Afficher les détails, puis sélectionnez **+ Nouvelle analyse** ou utilisez l’icône d’action rapide d’analyse sur la vignette source

1. Renseignez le *nom* et sélectionnez tous les types de ressources que vous souhaitez analyser dans cette source

    1. Vous pouvez laisser cette valeur sur *Tout* (ce paramètre inclut les futurs types de ressources qui n’existent pas encore dans cet abonnement ou ce groupe de ressources)
    1. Vous pouvez **sélectionner spécifiquement les types de ressources** que vous souhaitez analyser. Si vous choisissez cette option, les types de ressources qui pourront être créés à l’avenir dans cet abonnement ou ce groupe de ressources ne seront pas inclus pour les analyses, sauf si l’analyse est explicitement modifiée par la suite
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Analyser une source multiple Azure":::

1. Sélectionnez les informations d’identification pour vous connecter aux ressources dans votre source de données. 
    1. Vous pouvez sélectionner des **informations d’identification au niveau parent** comme le fichier MSI ou des informations d’identification d’un type de principal du service spécifique, que vous souhaitez utiliser pour tous les types de ressources dans l’abonnement ou le groupe de ressources
    1. Vous pouvez également **sélectionner spécifiquement le type de ressource et appliquer d’autres informations d’identification** pour ce type de ressource
    1. Ces informations d’identification seront considérées comme la méthode d’authentification pour toutes les ressources d’un type spécifique
    1. Vous devez définir les informations d’identification choisies sur les ressources afin de les analyser correctement comme décrit dans la [section](#Setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) ci-dessus
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom.
    1. Si vous conservez la valeur **Tout** pour l’option, les ressources futures de ce type seront également analysées lors des exécutions d’analyse ultérieures
    1. Si vous sélectionnez des comptes de stockage ou des bases de données SQL spécifiques, les ressources futures créées de ce type dans cet abonnement ou ce groupe de ressources ne seront pas incluses dans les analyses, sauf si l’analyse est explicitement modifiée par la suite
 
1.  Cliquez sur **Continuer** pour continuer. Nous allons tester l’accès pour vérifier si vous avez appliqué le fichier MSI Purview en tant que lecteur sur l’abonnement ou le groupe de ressources. Si un message d’erreur est généré, suivez les instructions [ici](#Setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Sélectionnez les **ensembles de règles d’analyse** pour chaque type de ressource choisi à l’étape précédente. Vous pouvez également créer des ensembles de règles d’analyse inline.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Sélection de l’ensemble de règles d’analyse multiple Azure":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution **hebdomadaire/mensuelle** ou **unique**

1. Vérifiez votre analyse et sélectionnez Enregistrer pour terminer la configuration   

## <a name="viewing-your-scans-and-scan-runs"></a>Affichage des analyses et des exécutions d’analyse

1. Pour afficher les détails de la source, cliquez sur **afficher les détails** dans la vignette sous la section Sources. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Détails d’une source multiple Azure"::: 

1. Pour afficher les détails de l’exécution de l’analyse, accédez à la page **Détails de l’analyse**.
    1. La *barre d’état* présente un bref résumé de l’état d’exécution des ressources enfants. Elle s’affiche au niveau de l’abonnement ou du groupe de ressources
    1. Le vert indique que l’opération a réussi, tandis que le rouge indique qu’elle a échoué. Le gris indique que l’analyse est en cours
    1. Vous pouvez cliquer sur chaque analyse pour afficher plus de détails

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Détails de l’analyse multiple Azure":::

1. Affichez un résumé des exécutions d’analyse récentes qui ont échoué en bas de la page Détails de la source. Vous pouvez également cliquer pour afficher des détails plus précis sur ces exécutions.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gérer vos analyses : modifier, supprimer ou annuler
Pour gérer ou supprimer une analyse, procédez comme suit :

- Accédez au centre d’administration. Sélectionnez Sources de données sous la section Sources et analyse, puis sélectionnez la source de données souhaitée

- Sélectionnez l’analyse que vous souhaitez gérer. Vous pouvez modifier l’analyse en sélectionnant Modifier

- Vous pouvez supprimer votre analyse en sélectionnant Supprimer
- Si une analyse est en cours d’exécution, il est également possible de l’annuler

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)    
