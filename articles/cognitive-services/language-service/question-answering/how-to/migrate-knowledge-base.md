---
title: Migrer des projets et des bases de connaissances - Réponses aux questions personnalisées
description: La migration d’un projet de réponses aux questions personnalisées nécessite l’exportation d’un projet à partir d’une ressource, puis l’importation dans une autre.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 46c092fa096c6f68299f6e0ac8254bfd25c7ce75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097684"
---
# <a name="migrate-projects-and-question-answer-sources"></a>Migrer des projets et des sources de réponses aux questions

Vous souhaiterez peut-être créer une copie de votre projet pour plusieurs raisons :

* Pour implémenter un processus de sauvegarde et de restauration
* Intégrer à votre pipeline CI/CD
* Lorsque vous souhaitez déplacer vos données vers différentes régions

## <a name="prerequisites"></a>Prérequis

* Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
* Une [ressource Language](https://aka.ms/create-language-resource) avec la fonctionnalité de réponses aux questions personnalisées activée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource de langue que vous avez sélectionnés au moment de créer la ressource.

## <a name="export-a-project"></a>Exporter un projet

L’exportation d’un projet vous permet de migrer ou de sauvegarder toutes les sources de réponses aux questions contenues dans un projet unique.

1. Connectez-vous à [Language Studio](https://language.azure.com/).
1. Sélectionnez la ressource de langue à partir de laquelle vous souhaitez migrer un projet.
1. Dans la page **Projects**, vous disposez d’options d’exportation dans deux formats, Excel ou TSV. Cela déterminera le contenu du fichier. Le fichier lui-même est exporté en tant que fichier .zip contenant toutes vos bases de connaissances.

## <a name="import-a-project"></a>Importer un projet  

1. Sélectionnez la ressource de langue, qui sera la destination de votre projet exporté.
1. Dans la page **Projects**, sélectionnez **Import**, puis choisissez le format utilisé lorsque vous avez choisi d’exporter. Accédez ensuite au fichier .zip local contenant votre projet exporté. Entrez un nom pour votre projet importé, puis sélectionnez **Done**.

## <a name="export-question-and-answers"></a>Exporter des réponses aux questions

1. Sélectionnez la ressource de langue à partir de laquelle vous souhaitez migrer une source de réponses aux questions.
1. Sélectionnez le projet qui contient la source de réponses aux questions que vous souhaitez exporter.
1. Dans la page de modification de la base de connaissances, sélectionnez l'icône représentant des points de suspension (`...`) à droite de la barre d’outils pour **activer le texte enrichi**. Vous avez la possibilité d’exporter au format Excel ou TSV.

## <a name="import-question-and-answers"></a>Importer des réponses aux questions

1. Sélectionnez la ressource de langue, qui sera la destination de votre source de réponse aux questions précédemment exportée.
1. Sélectionnez le projet dans lequel vous souhaitez importer une source de réponse aux questions.
1. Dans la page de modification de la base de connaissances, sélectionnez l'icône représentant des points de suspension (`...`) à droite de la barre d’outils pour **activer le texte enrichi**. Vous avez la possibilité d’importer un fichier Excel ou TSV.
1. Accédez à l’emplacement local du fichier avec l’option **Choose File**, puis sélectionnez **Done**.

<!-- TODO: Replace Link-->
### <a name="test"></a>Test

**Testez** la source de réponses aux questions en sélectionnant l’option **Test** dans la barre d’outils de la page **Edit knowledge base**, ce qui lancera le panneau de test. Découvrez comment [tester votre base de connaissances](../../../qnamaker/How-To/test-knowledge-base.md).

### <a name="deploy"></a>Déployer

<!-- TODO: Replace Link-->
**Déployez** la base de connaissances et créez un chatbot. Découvrez comment [déployer votre base de connaissances](../../../qnamaker/Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs"></a>Conversations

Il n’existe aucun moyen de migrer les journaux de conversation avec des projets ou des bases de connaissances. Si les journaux de diagnostic sont activés, les journaux de conversation sont stockés dans la ressource Azure Monitor associée.

## <a name="next-steps"></a>Étapes suivantes

<!-- TODO: Replace Link-->
> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../../../qnamaker/How-To/edit-knowledge-base.md)
