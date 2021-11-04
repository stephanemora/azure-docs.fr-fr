---
title: Créer une base de données de lac dans Azure Synapse à partir d’un modèle de base de données.
description: Découvrez comment explorer, personnaliser et créer une base de données de lac à partir d’un modèle de base de données.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: b657ed3d840345e36c38ef1a97723e67bfc5a38b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456819"
---
# <a name="how-to-create-a-lake-database-from-database-templates"></a>Guide pratique : Créer une base de données de lac à partir de modèles de base de données

Cet article explique comment utiliser les modèles de base de données Azure Synapse pour simplifier la création d’une [base de données de lac](./concepts-lake-database.md). L’utilisation de modèles de base de données a pour effet de fournir à votre base de données des informations sémantiques riches qui permettent aux utilisateurs finaux des données de comprendre plus facilement quelles données sont disponibles et comment les utiliser.

## <a name="prerequisites"></a>Prérequis

- Au minimum, des autorisations de rôle utilisateur Synapse sont requises pour explorer un modèle de base de données de lac à partir de la Galerie.
- Des autorisations d’administrateur ou de contributeur Synapse sont requises sur l’espace de travail Synapse pour créer une base de données de lac.
- Des autorisations de contributeur de données de blob de stockage sont requises sur le lac de données.

## <a name="create-lake-database-from-database-template"></a>Créer un base de données de lac à partir d’un modèle de base de données

1. Dans le hub **Accueil** de votre espace de travail Azure Synapse Analytics, sélectionnez **Centre des connaissances**, puis **Parcourir la Galerie**. Vous accédez à l’onglet **Modèles de base de données de lac**.
2. La catégorie **Modèles de base de données de lac** répertorie les modèles de base de données standardisés disponibles pour un secteur spécifique.
   1. Vous pouvez également accéder à l’onglet **Modèles de base de données de lac** à partir du hub **Données**, **+** Ajouter une nouvelle ressource, menu **Parcourir la galerie**.
3. Sélectionnez le secteur qui vous intéresse (par exemple, **Vente au détail**), puis choisissez **Continuer** pour accéder à l’exploration du modèle de données.
4. Vous accédez à la zone de dessin de la base de données où vous pouvez explorer les tables disponibles dans le modèle. Par défaut, la zone de dessin affiche une sélection des tables les plus utilisées dans ce modèle. La zone de dessin comporte différents outils pour vous aider à naviguer dans le diagramme des relations d’entité.
    - **Zoom pour ajuster** permettant de faire tenir toutes les tables figurant sur la zone de dessin dans la zone d’affichage
    - **Augmenter le zoom** pour effectuer un zoom avant sur la zone de dessin
    - **Réduire le zoom** pour effectuer un zoom arrière sur la zone de dessin
    - **Curseur de zoom** pour contrôler le niveau de zoom
    - **Aperçu du zoom** pour obtenir un aperçu de la zone de dessin
    - **Développer tout**/**Réduire tout** pour afficher plus ou moins de colonnes d’une table sur la zone de dessin
    - **Effacer la zone de dessin** pour effacer toutes les tables de la zone de dessin ![Page d’exploration de la zone de dessin présentant des exemples de tables et de contrôles.](./media/create-lake-database-from-lake-database-template/canvas-overview.png)

5. À gauche, s’affiche la liste des dossiers contenant les éléments du modèle que vous pouvez ajouter à la zone de dessin. Plusieurs contrôles sont disponibles pour vous aider.
    - **Zone de recherche** pour rechercher des tables sur la base d’un terme. Le terme sera recherché dans les tables, colonnes et descriptions du modèle.
    - **Filtrer les tables** pour sélectionner des sections entières du modèle à explorer
      - Les **modèles pour l’entreprise** sélectionnent des tables de chaque **secteur d’activité** pour afficher une sélection des meilleures.
      - Les **modèles de secteur d’activité** sélectionnent des tables principalement d’un seul **secteur d’activité**, mais incluent également des tables associées d’autres **secteurs d’activité**.
    - Les **secteurs d’activité** sont des dossiers contenant des tables liées à cette construction pour entreprise. Par exemple, le secteur d’activité Budget et prévision contient des tables liées à la gestion des budgets.
    - Vous pouvez développer des dossiers de secteur d’activité pour afficher les tables et activer la case à cocher pour les ajouter à la zone de dessin. 
    - Vous pouvez supprimer les tables sélectionnées à l’aide de la case à cocher.

6. Vous pouvez sélectionner une table sur la zone de dessin. Cela a pour effet d’ouvrir le volet des propriétés de la table contenant les onglets Général, Colonnes et Relations.
    - L’onglet Général contient des informations sur la table, telles que son nom et sa description.
    - L’onglet Colonnes contient des détails sur toutes les colonnes composant la table, tels que leurs noms et leurs types de données.
    - L’onglet Relations répertorie les relations entrantes et sortantes de la table avec d’autres tables sur la zone de dessin.
    
7. Pour ajouter rapidement des tables liées aux tables sur la zone de dessin, sélectionnez les points de suspension à droite du nom de la table, puis sélectionnez **Ajouter des tables associées**. Toutes les tables contenant des relations existantes sont ajoutées à la zone de dessin.

8. Une fois que la zone de dessin contient toutes les tables répondant à vos besoins, sélectionnez **Créer une base de données** pour poursuivre la création de la base de données de lac. La nouvelle base de données s’affiche dans le concepteur de base de données et vous pouvez la personnaliser selon les besoins de votre entreprise. 

9. Le concepteur de base de données comprend d’autres **Propriétés** à droite, qui doivent être configurées.
    - **Nom** donnez un nom à votre base de données. Les noms ne pouvant pas être modifiés après la publication de la base de données, veillez à choisir le nom correct.
    - **Description** L’attribution d’une description à votre base de données est facultative, mais elle permet aux utilisateurs de comprendre l’objectif de la base de données.
    - **Paramètres de stockage pour la base de données** Cette section contient les informations de stockage par défaut pour les tables de la base de données. Cette valeur par défaut est appliquée à chaque table de la base de données, sauf si elle est remplacée sur la table.
    - **Service lié** Service lié par défaut utilisé pour stocker vos données dans Azure Data Lake Storage. Le **service lié** par défaut associé à l’espace de travail Synapse s’affiche, mais vous pouvez le remplacer par n’importe quel compte de stockage ADLS de votre choix. 
    - **Dossier d’entrée** Utilisé pour définir le chemin d’accès par défaut du conteneur et du dossier au sein de ce service lié à l’aide de l’explorateur de fichiers.
    - **Format de données** Les bases de données de lac dans Azure Synapse prennent en charge les formats de fichier Parquet et de texte délimité comme formats de stockage pour les données.
> [!NOTE]
> Vous pouvez toujours remplacer les paramètres de stockage par défaut sur une table par la base de la table, et la valeur par défaut reste personnalisable. Si vous n’êtes pas sûr de votre choix, vous pouvez y revenir ultérieurement.
 
![Capture d’écran montrant le concepteur de base de données avec le volet des propriétés ouvert](./media/create-lake-database-from-lake-database-template/designer-overview.png)


10. Vous pouvez commencer à personnaliser les tables, colonnes et relations héritées du modèle de base de données. Vous pouvez également ajouter des tables, colonnes et relations personnalisées dans la base de données en fonction des besoins. Pour plus d’informations sur la modification d’une base de données Lake, consultez [Modifier une base de données de lac](./modify-lake-database.md).

11. Avec la base de données personnalisée, il est maintenant temps de le publier. Si vous utilisez une intégration Git avec votre espace de travail Synapse, vous devez valider vos modifications et les fusionner dans la branche Collaboration. [En savoir plus sur le contrôle de code source dans Azure Synapse](././cicd/../../cicd/source-control.md). Si vous utilisez le mode dynamique Synapse, vous pouvez sélectionner **Publier**.
     - Les erreurs sont validées dans votre base de données avant sa publication. Toutes les erreurs détectées s’affichent sous l’onglet Notifications, avec des instructions sur la façon de les corriger.
      
       ![Capture d’écran du volet de validation montrant des erreurs de validation dans la base de données](./media/create-lake-database-from-lake-database-template/validation-error.png)
     - La publication a pour effet de créer votre schéma de base de données dans le metastore Azure Synapse. Après publication, la base de données et les objets de table sont visibles par d’autres services Azure, et autorisent la circulation des métadonnées de votre base de données dans des applications telles que Power BI ou Purview.

12.  Vous avez maintenant créé une base de données de lac à l’aide d’un modèle de base de données de lac dans Azure Synapse. 

## <a name="next-steps"></a>Étapes suivantes

Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous. 
- [Modifier une base de données de lac](./modify-lake-database.md)
- [En savoir plus sur les bases de données de lac](./concepts-lake-database.md)
