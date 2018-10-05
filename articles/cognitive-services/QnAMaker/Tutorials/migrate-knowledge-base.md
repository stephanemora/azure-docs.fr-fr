---
title: Migrer des bases de connaissances en préversion - Qna Maker
titleSuffix: Azure Cognitive Services
description: Comment importer une base de connaissances
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 0cb8a185407c7b180a170f1f9b9d76aa28a24de5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031626"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrer une base de connaissances à l’aide des fonctions d’exportation-importation
QnA Maker a annoncé la disponibilité générale le 7 mai 2018 à la conférence \\\build\. QnA Maker GA dispose d’une nouvelle architecture qui repose sur Azure. Les bases de connaissances créées avec QnA Maker Free Preview doivent être migrées vers QnA Maker GA. QnA Maker Preview sera déconseillé à partir de novembre 2018. Pour plus d’informations sur les modifications apportées dans QnA Maker GA, consultez le [billet de blog](https://aka.ms/qnamakerga-blog) de QnA Maker.

QnA Maker propose maintenant un [modèle de tarification](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Prérequis
> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
> * Configurer un nouveau [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrer une base de connaissances à partir du portail QnA Maker Preview
1. Accédez au [portail QnA Maker en préversion](https://aka.ms/qnamaker-old-portal
), puis cliquez sur **My services** (Mes services).
2. Sélectionnez la base de connaissances que vous souhaitez migrer en cliquant sur l’icône de modification.

    ![Modifier la base de connaissances](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Cliquez sur **Download knowledge base** (Télécharger la base de connaissances) pour télécharger un fichier .tsv qui contient le contenu de votre base de connaissances : questions, réponses, métadonnées et nom des sources de données à partir desquelles elles ont été extraites.

    ![Télécharger la base de connaissances](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Connectez-vous au [portail QnA Maker](https://qnamaker.ai) avec vos informations d’identification Azure, puis cliquez sur **Create new service** (Créer un service).

    ![Créer la base de connaissances ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Si vous n’avez pas encore créé de service QnA Maker, sélectionnez **Create a QnA service** (Créer un service QnA). Sinon, choisissez un service QnA Maker dans les listes déroulantes à l’étape 2. Sélectionnez le service QnA Maker qui hébergera la base de connaissances.

    ![Créer un service QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Créer une base de connaissances vide 

    ![Définir les sources de données](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Donnez un **nom** à votre service. Les noms dupliqués et les caractères spéciaux sont acceptés.
    - Ignorez le téléchargement des fichiers ou URL, dans la mesure où vous souhaitez utiliser les données de votre base de connaissances Preview. Pour le moment, vous allez créer une base de connaissances vide.

7. Sélectionnez **Créer**.

    ![Créer la base de connaissances](../media/qnamaker-how-to-create-kb/create-kb.png)

8. Dans cette nouvelle base de connaissances, ouvrez l’onglet **Paramètres**, puis cliquez sur **Importer une base de connaissances**. Cette action importe les questions, les réponses et les métadonnées, tout en conservant le nom des sources de données à partir desquelles elles ont été extraites.

   ![Importer une base de connaissances](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Testez** la nouvelle base de connaissances à l’aide du panneau de test. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).
10. **Publiez** la base de connaissances. Découvrez comment [publier votre base de connaissances](../How-To/publish-knowledge-base.md).
11. Utilisez le point de terminaison ci-dessous dans votre code d’application ou de bot. Découvrez comment [créer un bot QnA](../Tutorials/create-qna-bot.md) ici.

    ![Valeurs QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

À ce stade, tout le contenu de la base de connaissances (questions, réponses et métadonnées, ainsi que le nom des fichiers sources et les URL) est importé dans la nouvelle base de connaissances. 

## <a name="chatlogs-and-alterations"></a>Conversations et modifications
Les modifications (synonymes) ne sont pas importées automatiquement. Utilisez les [API V2](https://aka.ms/qnamaker-v2-apis) pour exporter les modifications de la pile d’aperçu et les [API V4](https://aka.ms/qnamaker-v4-apis) pour les replacer dans la nouvelle pile.

Il n’existe aucun moyen de migrer les conversations, étant donné que la nouvelle pile utilise Application Insights pour le stockage des conversations. Vous pouvez cependant télécharger les conversations à partir du [portail en préversion](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-To/edit-knowledge-base.md)
