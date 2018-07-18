---
title: Guide pratique pour créer une base de connaissances - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: Découvrez comment créer une base de connaissances.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370877"
---
# <a name="create-a-knowledge-base"></a>Créer une base de connaissances

QnA Maker facilite considérablement l’intégration de vos sources de données existantes pour créer une base de connaissances. Vous pouvez créer une base de connaissances QnA Maker en y intégrant, automatiquement ou manuellement, du contenu existant, tel que des pages FAQ, des manuels de produits ou des documents structurés.

## <a name="steps"></a>Étapes

1. Pour commencer, connectez-vous au [portail QnA Maker](https://qnamaker.ai) avec vos informations d’identification Azure, puis cliquez sur **Créer un service**.

    ![Créer la base de connaissances ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Si vous n’avez pas encore créé de service QnA Maker, sélectionnez **Create a QnA service** (Créer un service QnA). Sinon, choisissez un service QnA Maker dans les listes déroulantes à l’étape 2. Sélectionnez le service QnA Maker qui hébergera la base de connaissances.

    ![Créer un service QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Entrez les informations suivantes pour créer la base de connaissances.

    ![Définir les sources de données](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Donnez un **nom** à votre service. Les noms dupliqués et les caractères spéciaux sont acceptés.
    - Collez les URL des sources d’extraction. Pour plus d’informations sur les types de sources prises en charge, [cliquez ici](../Concepts/data-sources-supported.md).
    - Vous pouvez également charger les fichiers à partir desquels les données sont extraites. Consultez les [prix](https://aka.ms/qnamaker-pricing
) pour savoir combien de documents vous pouvez ajouter.
    - Si vous préférez ajouter manuellement des entités QnA, vous pouvez ignorer l’étape de liaison des fichiers.

4. Sélectionnez **Créer**.

    ![Créer la base de connaissances](../media/qnamaker-how-to-create-kb/create-kb.png)

5. L’extraction des données peut prendre quelques minutes.

    ![Extraction](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Quand votre base de connaissances a été créée, vous êtes redirigé vers la page **Base de connaissances**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Importer une base de connaissances](../Tutorials/migrate-knowledge-base.md)
