---
title: 'Démarrage rapide : Charger des données en masse avec un pool SQL dédié'
description: Utilisez Synapse Studio pour charger des données en masse dans un pool SQL dédié dans Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 312c57c103bf733bc72c5de1d22ab3239d5b5e96
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484665"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Démarrage rapide : Chargement en masse avec Synapse SQL

Le chargement de données est une tâche aisée avec l’Assistant Chargement en masse dans Synapse Studio. L’assistant Chargement en masse va vous guider tout au long de la création d’un script T-SQL avec [l’instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) pour charger en masse des données. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Points d’entrée de l’assistant Chargement en masse

Vous pouvez facilement charger des données en masse à l’aide de pools SQL dédiés en cliquant avec le bouton droit sur les zones suivantes de Synapse Studio :

- Un fichier ou un dossier à partir d’un compte de stockage Azure attaché à votre espace de travail en ![cliquant avec le bouton droit sur un fichier ou un dossier à partir d’un compte de stockage](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Prérequis

- Cet Assistant génère une instruction COPY, qui utilise le pass-through Azure AD pour l’authentification. Votre [utilisateur Azure AD doit avoir accès](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) à l’espace de travail avec au moins le rôle Azure Contributeur aux données Blob du stockage sur le compte ADLS Gen2. 

- Vous devez disposer des [autorisations requises pour utiliser l’instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) et les autorisations Create table si vous créez une table pour le chargement.

- Le service lié associé au compte ADLS Gen2 **doit avoir accès au fichier**/**dossier** à charger. Par exemple, si le mécanisme d’authentification du service lié est Managed Identity, l’identité managée de l’espace de travail doit avoir au moins l’autorisation Lecteur des données Blob du stockage sur le compte de stockage.

- Si le réseau virtuel est activé sur votre espace de travail, assurez-vous que le runtime intégré associé aux services liés au compte ADLS Gen2 pour les données sources et l’emplacement du fichier d’erreur est activé pour la création interactive. La création interactive est requise pour la détection de schéma automatique, l’aperçu du contenu du fichier source et le parcours des comptes de stockage ADLS Gen2 dans l’Assistant.

### <a name="steps"></a>Étapes

1. Sélectionnez le compte de stockage et le fichier ou dossier à partir duquel vous effectuez le chargement dans le volet Emplacement de stockage source. L’Assistant tente automatiquement de détecter les fichiers Parquet. Si le type de fichier Parquet ne peut pas être confirmé, le format de texte délimité (CSV) est utilisé par défaut.

   ![Sélection de l’emplacement source](./sql/media/bulk-load/bulk-load-source-location.png)

2. Sélectionnez les paramètres de format de fichier, notamment le compte de stockage dans lequel vous souhaitez écrire les lignes rejetées (fichier d’erreur). Actuellement, seuls les fichiers CSV et Parquet sont pris en charge.

    ![Sélection des paramètres de format de fichier](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Vous pouvez sélectionner « Aperçu des données » pour voir comment l’instruction COPY analyse le fichier pour vous aider à configurer les paramètres de format de fichier. Sélectionnez « Aperçu des données » chaque fois que vous modifiez un paramètre de format de fichier pour voir comment l’instruction COPY analyse le fichier avec le paramètre mis à jour : ![Aperçu des données](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - L’affichage de l’aperçu des données avec des indicateurs de fin de champ à plusieurs caractères n’est pas pris en charge dans l’Assistant Chargement en masse. L’Assistant Chargement en masse affiche un aperçu des données dans une seule colonne lorsqu’un terminateur de champ à plusieurs caractères est spécifié. 
> - La spécification d’indicateurs de fin de ligne à plusieurs caractères est prise en charge dans l’instruction COPY. Toutefois, elle n’est pas prise en charge dans l’Assistant Chargement en masse, où une erreur sera levée.

4. Sélectionnez le pool SQL dédié que vous utilisez pour charger, notamment si la charge concerne une table existante ou une nouvelle table : ![Sélection de l’emplacement cible](./sql/media/bulk-load/bulk-load-target-location.png)

5. Sélectionnez « Configurer le mappage de colonnes » pour vérifier que vous disposez du mappage de colonnes approprié. Notez que les noms de colonnes sont automatiquement détectés si l’option « Déduire les noms de colonnes » a été activée. Pour les nouvelles tables, la configuration du mappage de colonnes est essentielle pour la mise à jour des types de données des colonnes cibles : ![Configuration du mappage de colonnes](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Sélectionnez « Ouvrir un script » et un script T-SQL sera généré avec l’instruction COPY pour charger à partir de votre lac de données : ![Ouverture du script SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les fonctionnalités de COPY, consultez l’article [Instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- Consultez l’article [Vue d’ensemble du chargement de données](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)
