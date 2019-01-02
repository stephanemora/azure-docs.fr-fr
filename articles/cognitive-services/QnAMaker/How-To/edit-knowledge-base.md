---
title: Modifier une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d40886f54083a1619635f31af57841138f1706d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086604"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Modifier une base de connaissances dans QnA Maker

QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.

## <a name="edit-your-knowledge-base-content"></a>Modifier le contenu de votre base de connaissances

1.  Sélectionnez **Mes bases de connaissances** dans la barre de navigation supérieure. 

    Vous pouvez voir tous les services, que vous avez créés ou qui ont été partagés avec vous, triés dans l’ordre décroissant de la date de **dernière modification**.

    ![Mes bases de connaissances](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Sélectionnez une base de connaissances particulière pour y apporter des modifications.
 
1. Sélectionnez **Paramètres**. Ici, vous pouvez modifier le champ obligatoire Nom du service.
  
    |Objectif|Action|
    |--|--|
    |Ajouter une URL|Vous pouvez ajouter de nouvelles URL pour ajouter du nouveau contenu de FAQ à la base de connaissances en cliquant sur le lien **Manage knowledge base -> '+ Add URL'** (Gérer la base de connaissances -> « + Ajouter une URL »).|
    |Supprimer une URL|Vous pouvez supprimer des URL existantes en sélectionnant l’icône supprimer, la corbeille.|
    |Actualiser le contenu de l’URL|Si vous souhaitez que la base de connaissances analyse le contenu le plus récent des URL existantes, cochez la case **Refresh** (Actualiser). Cela met à jour la base de connaissances avec le contenu d’URL le plus récent.|
    |Ajouter un fichier|Vous pouvez ajouter un document pris en charge pour qu’il fasse partie de la base de connaissances en sélectionnant **Manage knowledge base** (Gérer la base de connaissance), puis -> **+ Add File** (+ Ajouter un fichier).|
    |Importer|Vous pouvez également importer n’importe quelle base de connaissances existante en sélectionnant le bouton **Import Knowledgebase** (Importer la base de connaissances). |
    |Mettre à jour|La mise à jour de la base de connaissances dépend du **niveau tarifaire de gestion** utilisé lors de la création du service QnA Maker associé à votre base de connaissances. Vous pouvez également mettre à jour le niveau de gestion à partir du portail Azure si nécessaire.

1. Une fois que vous avez fini d’apporter des modifications à la base de connaissances, sélectionnez **Save and train** (Enregistrer et former) dans le coin supérieur droit de la page afin de conserver les modifications.    

    ![Enregistrer et former](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Si vous quittez la page avant de sélectionner **Save and train** (Enregistrer et former), toutes les modifications seront perdues.

## <a name="add-a-qna-pair"></a>Ajouter une paire QnA

Sélectionnez **Add QnA pair** (Ajouter une paire QnA) pour ajouter une nouvelle ligne à la table de base de connaissances.

![Ajouter une paire QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Supprimer une paire QnA

Pour supprimer un QnA, cliquez sur l’icône **Supprimer** à l’extrême droite de la ligne QnA. Il s’agit d’une opération définitive. Elle ne peut pas être annulée. Envisagez d’exporter votre base de connaissances à partir de la page **Publier** avant de supprimer des paires. 

![Supprimer une paire QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Ajoutez des questions alternatives à une paire QnA existante pour améliorer la probabilité d’une correspondance à une requête utilisateur.

![Ajouter des questions alternatives](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Ajouter des métadonnées


Ajouter des paires de métadonnées en sélectionnant l’icône de filtre

![Ajouter des métadonnées](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Veillez à enregistrer et former régulièrement la base de connaissances après avoir apporté des modifications pour éviter de perdre des modifications.

## <a name="manage-large-knowledge-bases"></a>Gérer de grandes bases de connaissances

1. Les QnA sont **regroupés** par la source de données de laquelle ils ont été extraits. Vous pouvez développer ou réduire la source de données.
2. Vous pouvez **rechercher** dans la base de connaissances en saisissant dans la zone de texte en haut de la table de Base de connaissances. Cliquez sur Entrée pour effectuer des recherches sur le contenu de la question, de la réponse ou des métadonnées. Cliquez sur l’icône X pour supprimer le filtre de recherche.
3. La **pagination** vous permet de gérer de grandes bases de connaissances

    ![Rechercher, paginer, regrouper](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="delete-knowledge-bases"></a>Supprimer les bases de connaissances

La suppression d’une base de connaissances (KB) est une opération définitive. Elle ne peut pas être annulée. Avant de supprimer une base de connaissances, exportez-la à partir de la page **Paramètres** page du portail QnA Maker. 

Si vous partagez votre base de connaissances avec d’autres [collaborateurs](collaborate-knowledge-base.md) puis que vous la supprimez, tous les utilisateurs perdent l’accès à cette base de connaissances. 

## <a name="delete-azure-resources"></a>Supprimer les ressources Azure 

Si vous supprimez l’une des ressources Azure utilisées pour vos bases de connaissances QnA Maker, celles-ci ne fonctionneront plus. Avant de supprimer des ressources, veillez à exporter vos bases de connaissances à partir de la page **Paramètres**. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur une base de connaissances](./collaborate-knowledge-base.md)
