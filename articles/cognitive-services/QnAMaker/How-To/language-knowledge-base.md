---
title: Base de connaissances dans une langue autre que l’anglais - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prend en charge le contenu de la base de connaissances dans de nombreuses langues. Toutefois, chaque service QnA Maker doit être réservé à une seule langue. La première base de connaissances créée ciblant un service QnA Maker particulier définit la langue de ce service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961476"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Prise en charge linguistique du contenu de la base de connaissances pour QnA Maker

QnA Maker prend en charge le contenu de la base de connaissances dans de nombreuses langues. Toutefois, chaque service QnA Maker doit être réservé à une seule langue. La première base de connaissances créée ciblant un service QnA Maker particulier définit la langue de ce service. Voir la [ici](../Overview/languages-supported.md) la liste complète des langues prises en charge.

La langue est automatiquement reconnue à partir du contenu des sources de données en cours d’extraction. Une fois que vous créez un nouveau Service QnA Maker et une base de connaissances dans ce service, vous pouvez vérifier que la langue a été correctement définie.

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **groupes de ressources** et accédez au groupe de ressources où le service QnA Maker est déployé, puis sélectionnez la ressource **Recherche Azure**.

    ![Sélectionnez la ressource Recherche Azure](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Sélectionnez **Index**, puis l’index **testkb**. Il s’agit du premier index Recherche Azure créé. Ill contient le contenu enregistré de toutes les bases de connaissances de ce service. 

1. Sélectionnez **Champs** pour afficher les champs dans l’index.

1. La colonne _Analyseur_ des champs `questions` et `answer` est définie avec un langage spécifique. Ce langage a été détecté automatiquement lors de l’étape de création de base de connaissances à partir des fichiers et des URL importés. Elle ne peut pas être modifiée une fois que la ressource est créée.

    ![Analyseur sélectionné](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un bot QnA avec Azure Bot Service](../Tutorials/create-qna-bot.md)
