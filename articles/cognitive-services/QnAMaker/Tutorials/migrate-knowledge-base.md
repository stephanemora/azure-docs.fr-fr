---
title: Migrer des bases de connaissances - QnA Maker
description: La migration d’une base de connaissances nécessite l’exportation d’une base de connaissances, puis l’importation dans une autre.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ea0a02366f2c2d2c3fd656d9a6dbce111d632422
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121198"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrer une base de connaissances à l’aide des fonctions d’exportation-importation

Vous pouvez souhaiter créer une copie de votre base de connaissances pour plusieurs raisons :

* Copier une base de connaissances de QnA Maker GA vers Réponses aux questions personnalisées 
* Pour implémenter un processus de sauvegarde et de restauration 
* Intégrer à votre pipeline CI/CD 
* Lorsque vous souhaitez déplacer vos données vers différentes régions

## <a name="prerequisites"></a>Prérequis

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

> * Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) créée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource QnA que vous avez sélectionnés au moment de créer la ressource.
> * Configurer un nouveau [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (préversion)](#tab/v2)

> * Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Une [ressource Analyse de texte](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) avec la fonctionnalité Réponses aux questions personnalisées activée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource Analyse de texte que vous avez sélectionnés au moment de créer la ressource.
> * Configurer [Réponses aux questions personnalisées](../How-To/set-up-qnamaker-service-azure.md)

---

## <a name="export-a-knowledge-base"></a>Exporter une base de connaissances
1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai).
1. Sélectionnez la base de connaissances que vous souhaitez migrer.

1. Sur la page **Paramètres**, vous disposez des options d’exportation de **QnAs**, **Synonymes** ou **Réplica de base de connaissances**. Vous pouvez choisir de télécharger les données dans un fichier. tsv/.xlsx.

   1. **QnAs** : lors de l’exportation de QnAs, toutes les paires QnA (avec des questions, des réponses, des métadonnées, des invites de suivi et les noms des sources de données) sont téléchargées. Les ID QnA exportés avec les questions et réponses peuvent être utilisés pour mettre à jour une paire QnA spécifique à l’aide de l’[API de mise à jour](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). L’ID QnA d’une paire QnA spécifique reste inchangé après plusieurs opérations d’exportation.
   2. **Synonymes** : vous pouvez exporter des synonymes qui ont été ajoutés à la base de connaissances.
   4. **Réplica de base de connaissances** : Si vous souhaitez télécharger l’intégralité de la base de connaissances avec des synonymes et d’autres paramètres, vous pouvez choisir cette option.

## <a name="import-a-knowledge-base"></a>Importer une base de connaissances
1. Cliquez sur **Créer une base de connaissances** dans le menu supérieur du portail qnamaker.ai, puis créez une base de connaissances _vide_ en n’ajoutant aucune URL ni aucun fichier. Utilisez le nom de votre choix pour la nouvelle base de connaissances, puis cliquez sur  **Créer votre base de connaissances**. 

1. Dans cette nouvelle base de connaissances, ouvrez l’onglet **Paramètres** puis, sous _Importer une base de connaissances_, sélectionnez l’une des options suivantes : **QnAs**, **Synonymes** ou **Réplica de base de connaissances**. 

   1. **QnAs** : cette option importe toutes les paires QnA. **Les paires QnA créées dans la nouvelle base de connaissances portent le même ID QnA que celui présent dans le fichier exporté**. Vous pouvez vous reporter à [SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas), [SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv) pour importer des QnAs.
   2. **Synonymes** : cette option peut être utilisée pour importer des synonymes dans la base de connaissances. Vous pouvez vous reporter à [SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms), [SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv) pour importer des synonymes.
   3. **Réplica de base de connaissances** : cette option peut être utilisée pour importer un réplica de base de connaissances avec QnAs, synonymes et Paramètres. Pour plus d’informations, vous pouvez vous reporter à [KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica), [KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv). Si vous souhaitez également ajouter du contenu non structuré au réplica, reportez-vous à [CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica).

      Du contenu QnAs ou non structuré est requis lors de l’importation de réplicas. Les documents non structurés sont valides uniquement pour Réponses aux questions personnalisées.
      Le fichier de synonymes n’est pas obligatoire lors de l’importation de réplicas.
      Le fichier de paramètres est obligatoire lors de l’importation de réplicas.

         |Paramètres|Mise à jour autorisée lors de l’importation vers la base de connaissances QnA Maker ?|Mise à jour autorisée lors de l’importation vers la base de connaissances Réponses aux questions personnalisées ?|
         |:--|--|--|
         |DefaultAnswerForKB|Non|Oui|
         |EnableActiveLearning (True/False)|Oui|Non|
         |EnableMultiTurnExtraction (True/False)|Oui|Oui|
         |DefaultAnswerforMultiturn|Oui|Oui|
         |Langage|Non|Non|

1. **Testez** la nouvelle base de connaissances à l’aide du panneau de test. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).

1. **Publiez** la base de connaissances et créez un chatbot. Découvrez comment [publier votre base de connaissances](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

   > [!div class="mx-imgBorder"]
   > ![Migrer une base de connaissances](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migrer par programmation une base de connaissances à partir de QnA Maker

Le processus de migration est disponible par programmation à l’aide des API REST suivantes :

**Export**

* [API Télécharger la base de connaissances](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importer**

* [API Remplacer (recharger avec le même ID de base de connaissances)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [API Créer (charger avec un nouvel ID de base de connaissances)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>Conversations
Il n’existe aucun moyen de migrer les conversations, étant donné que la nouvelle base de connaissances utilise Application Insights pour le stockage des conversations.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-To/edit-knowledge-base.md)
