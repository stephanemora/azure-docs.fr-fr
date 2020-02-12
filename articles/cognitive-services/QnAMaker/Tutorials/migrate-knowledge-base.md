---
title: Migrer des bases de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: La migration d’une base de connaissances nécessite l’exportation d’une base de connaissances, puis l’importation dans une autre.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902059"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrer une base de connaissances à l’aide des fonctions d’exportation-importation

La migration d’une base de connaissances nécessite l’exportation d’une base de connaissances, puis l’importation dans une autre.

## <a name="prerequisites"></a>Conditions préalables requises

* Avant de commencer, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Configurer un nouveau [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrer une base de connaissances à partir de QnA Maker
1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai).
1. Sélectionnez la base de connaissances d'origine que vous souhaitez migrer.

1. Sur la page **Paramètres**, sélectionnez **Exporter la base de connaissances** pour télécharger un fichier .tsv qui contient le contenu de votre base de connaissances : questions, réponses, métadonnées, invites de suivi et noms des sources de données à partir desquelles elles ont été extraites.

1. Sélectionnez **Créer une base de connaissances** dans le menu supérieur, puis créez une base de connaissances _vide_. Elle est vide car lorsque vous la créez, vous n'y ajoutez ni URL ni fichiers. Ceux-ci sont ajoutés lors de l’étape d’importation, après la création.

    Configurez la base de connaissances. Définissez le nom de la nouvelle base de connaissances uniquement. Les noms dupliqués et les caractères spéciaux sont acceptés.

    Ne sélectionnez rien à l’étape 4, car ces valeurs seront remplacées lorsque vous importerez le fichier.

1. À l’étape 5, sélectionnez **Créer**.

1. Dans cette nouvelle base de connaissances, ouvrez l’onglet **Paramètres**, puis sélectionnez **Importer une base de connaissances**. Cette action importe les questions, les réponses, les métadonnées et les invites de suivi, tout en conservant le nom des sources de données à partir desquelles elles ont été extraites.

   > [!div class="mx-imgBorder"]
   > [![Importer une base de connaissances](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testez** la nouvelle base de connaissances à l’aide du panneau de test. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).
1. **Publiez** la base de connaissances. Découvrez comment [publier votre base de connaissances](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Utilisez le point de terminaison dans votre code d’application ou de bot. Découvrez comment [créer un bot QnA](../Tutorials/create-qna-bot.md) ici.

    ![Valeurs QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    À ce stade, tout le contenu de la base de connaissances (questions, réponses et métadonnées, ainsi que le nom des fichiers sources et les URL) est importé dans la nouvelle base de connaissances.

## <a name="chat-logs-and-alterations"></a>Conversations et modifications
Les modifications qui ne respectent pas la casse (synonymes) ne sont pas importées automatiquement. Utilisez les [API V4](https://go.microsoft.com/fwlink/?linkid=2092179) pour déplacer les modifications dans nouvelle la base de connaissances.

Il n’existe aucun moyen de migrer les conversations, étant donné que la nouvelle base de connaissances utilise Application Insights pour le stockage des conversations.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-To/edit-knowledge-base.md)
