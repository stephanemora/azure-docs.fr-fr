---
title: Analyser plusieurs sources dans Azure Purview
description: Découvrez comment analyser l’intégralité d’un abonnement Azure ou d’un groupe de ressources dans votre catalogue de données Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: f3503dd986f037310b2b24dec535cc05b9d4c4a4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127494"
---
# <a name="register-and-scan-multiple-sources-in-azure-purview"></a>Inscrire et analyser plusieurs sources dans Azure Purview

Cet article explique comment inscrire plusieurs sources (abonnements ou groupes de ressources Azure) dans Azure Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez analyser plusieurs sources pour capturer les métadonnées et le schéma sur la plupart des types de ressources Azure pris en charge par Azure Purview. Azure Purview classe également les données automatiquement selon des règles de classification système et personnalisées.

## <a name="prerequisites"></a>Prérequis

- Avant d’inscrire des sources de données, créez un compte Azure Purview. Pour plus d’informations, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
- Assurez-vous que vous êtes administrateur de la source de données Azure Purview. Vous devez également être propriétaire ou administrateur de l’accès utilisateur pour ajouter un rôle à un abonnement ou groupe de ressources.
- Configurez l’authentification comme décrit dans les sections suivantes.

### <a name="set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Configurer l’authentification pour l’énumération des ressources dans un abonnement ou groupe de ressources

1. Accédez à l’abonnement ou au groupe de ressources dans le portail Azure.  
1. Sélectionnez  **Contrôle d’accès (IAM)**   dans le menu de gauche. 
1. Sélectionnez **+Ajouter**. 
1. Dans la zone **Sélectionner l’entrée**, sélectionnez le rôle **Lecteur** et entrez le nom de votre compte Azure Purview (il s’agit de son fichier MSI). 
1. Sélectionnez **Enregistrer** pour terminer l’attribution de rôle.

### <a name="set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Configurer l’authentification pour l’analyse des ressources dans un abonnement ou un groupe de ressources

Il existe deux façons de configurer l’authentification pour plusieurs sources dans Azure :

- Identité managée
- Principal du service

Vous devez configurer l’authentification sur chaque ressource dans votre abonnement ou votre groupe de ressources que vous souhaitez inscrire et analyser. Les types de ressources Stockage Azure (Stockage Blob Azure et Azure Data Lake Storage Gen2) vous facilitent la tâche en vous permettant d’ajouter le fichier MSI ou le principal de service au niveau de l’abonnement ou du groupe de ressources en tant que lecteur des données blob du stockage. Les autorisations s’étendent ensuite à chaque compte de stockage de cet abonnement ou groupe de ressources. Pour tous les autres types de ressources, vous devez appliquer le fichier MSI ou le principal du service sur chaque ressource ou créer un script pour ce faire. 

Pour savoir comment ajouter des autorisations sur chaque type de ressource au sein d’un abonnement ou groupe de ressources, consultez les ressources suivantes :
    
- [Stockage Blob Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-multiple-sources"></a>Inscrire plusieurs sources

Pour inscrire plusieurs sources dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Azure Purview.
1. Sélectionnez **Sources** dans le menu de gauche.
1. Sélectionnez **Enregistrer**.
1. Sous **Inscrire des sources**, sélectionnez **Azure (Multiple)** .

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Capture d’écran montrant la vignette pour Azure Multiple à l’écran pour l’inscription de plusieurs sources.":::
1. Sélectionnez **Continuer**.
1. Sur l’écran **Inscrire des sources (Azure)** , procédez comme suit :

   1. Dans la zone **Nom**, entrez le nom sous lequel la source de données apparaîtra dans le catalogue. 
   1. Dans la zone **Groupe d’administration**, vous pouvez choisir groupe d’administration sur lequel effectuer le filtrage.
   1. Dans les zones de liste déroulante **Abonnement** et **Groupe de ressources**, sélectionnez un abonnement ou un groupe de ressources spécifique. L’étendue d’inscription sera définie sur l’abonnement ou le groupe de ressources sélectionné.  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Capture d’écran montrant les zones de sélection d’un abonnement et d’un groupe de ressources.":::
   1. Dans la zone **Sélectionner une collection**, sélectionnez une collection ou créez-en une (facultatif).
   1. Sélectionnez **Terminer** pour inscrire la source de données.


## <a name="create-and-run-a-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Accédez à la section **Sources**.
1. Sélectionnez la source de données que vous avez inscrite.
1. Cliquez sur **Afficher les détails** >  **+ Nouvelle analyse** ou utilisez l’icône d’action rapide d’**analyse** sur la vignette source.
1. Pour **Nom**, entrez le nom.
1. Pour **Type**, sélectionnez les types de ressources que vous souhaitez analyser au sein de cette source. Choisissez une de ces options :

    - Conserver **Tout**. Cette sélection inclut les futurs types de ressources qui n’existent pas encore dans cet abonnement ou ce groupe de ressources.
    - Utilisez les zones pour sélectionner spécifiquement les types de ressources que vous souhaitez analyser. Si vous choisissez cette option, les types de ressources qui pourront être créés à l’avenir dans cet abonnement ou ce groupe de ressources ne seront pas inclus pour les analyses, sauf si l’analyse est explicitement modifiée par la suite.
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Capture d’écran montrant les options d’analyse de plusieurs sources.":::

1. Sélectionnez les informations d’identification pour vous connecter aux ressources dans votre source de données. 
    - Vous pouvez sélectionner des informations d’identification au niveau parent comme un fichier MSI ou sélectionner des informations d’identification pour un type de principal de service spécifique. Vous pouvez ensuite utiliser ces informations d’identification pour tous les types de ressources de l’abonnement ou du groupe de ressources.
    - Vous pouvez sélectionner spécifiquement le type de ressource et appliquer d’autres informations d’identification pour ce type de ressource.
    
    Ces informations d’identification seront considérées comme la méthode d’authentification pour toutes les ressources d’un type spécifique. Vous devez définir les informations d’identification choisies sur les ressources afin de les analyser correctement comme décrit [précédemment dans cet article](#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group).
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom :
    - Si vous conservez la valeur **Tout** pour l’option, les ressources futures de ce type seront également analysées lors des exécutions d’analyse ultérieures.
    - Si vous sélectionnez des comptes de stockage ou des bases de données SQL spécifiques, les ressources futures de ce type créées dans cet abonnement ou ce groupe de ressources ne seront pas incluses dans les analyses, sauf si l’analyse est explicitement modifiée par la suite.
 
1. Sélectionnez **Continuer** pour poursuivre. Azure Purview l’accès pour vérifier si vous avez appliqué le fichier MSI Azure Purview en tant que lecteur sur l’abonnement ou le groupe de ressources. Si vous recevez un message d’erreur, suivez [ces instructions](#set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group) pour y remédier.

1. Sélectionnez les ensembles de règles d’analyse pour chaque type de ressource choisi à l’étape précédente. Vous pouvez également créer des ensembles de règles d’analyse inline.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Capture d’écran montrant des règles d’analyse pour chaque type de ressource.":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution hebdomadaire, mensuelle ou unique.

1. Vérifiez votre analyse et sélectionnez **Enregistrer** pour terminer la configuration. 

## <a name="view-your-scans-and-scan-runs"></a>Afficher vos analyses et exécutions d’analyse

1. Pour afficher les détails de la source, sélectionnez **Afficher les détails** dans la vignette située sous la section **Sources**. 

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Capture d’écran montrant les détails de la source."::: 

1. Pour afficher les détails de l’exécution de l’analyse, accédez à la page **Détails de l’analyse**.
   
    La *barre d’état* présente un bref résumé de l’état d’exécution des ressources enfants. Elle s’affiche au niveau de l’abonnement ou du groupe de ressources. Voici la signification des différentes couleurs :
    
    - Vert : l’analyse a réussi.
    - Rouge : l’analyse a échoué. 
    - Gris : l’analyse est toujours en cours.
   
    Vous pouvez sélectionner chaque analyse pour afficher des détails plus précis.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Capture d’écran montrant les détails de l’analyse.":::

1. Affichez un résumé des exécutions d’analyse récentes qui ont échoué en bas des détails de la source. Vous pouvez également afficher des détails plus précis sur ces exécutions.

## <a name="manage-your-scans-edit-delete-or-cancel"></a>Gérer vos analyses : modifier, supprimer ou annuler
Pour gérer une analyse, procédez comme suit :

1. Accédez au centre de gestion.
1. Sélectionnez **Sources de données** sous la section **Sources et analyse**, puis sélectionnez la source de données souhaitée
1. Sélectionnez l’analyse que vous souhaitez gérer. Ensuite : 

   - Vous pouvez modifier l’analyse en sélectionnant **Modifier**.
   - Vous pouvez supprimer l’analyse en sélectionnant **Supprimer**.
   - Si l’analyse est en cours d’exécution, vous pouvez l’annuler en sélectionnant **Annuler**.

## <a name="next-steps"></a>Étapes suivantes

- [Naviguer dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Effectuer une recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)    
