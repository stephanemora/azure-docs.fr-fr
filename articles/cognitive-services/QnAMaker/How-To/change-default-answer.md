---
title: Obtenir la réponse par défaut - QnA Maker
description: La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être remplacer la réponse par défaut standard.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097096"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Modifier la réponse par défaut pour une ressource QnA Maker

La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être remplacer la réponse par défaut standard.

## <a name="change-default-answer-in-the-azure-portal"></a>Modifier la réponse par défaut dans le Portail Azure

1. Accédez au [portail Azure](https://portal.azure.com) et au groupe de ressources qui représente le service QnA Maker que vous avez créé.

2. Cliquez pour ouvrir **App Service**.

    ![Sur le portail Azure, accéder à App Service pour QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Cliquez sur **Paramètres de l’application** et modifiez le champ **DefaultAnswer** pour entrer la réponse par défaut souhaitée. Cliquez sur **Enregistrer**.

    ![Sélectionner Paramètres de l’application, puis modifier DefaultAnswer pour QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Redémarrer votre App service

    ![Après avoir modifié DefaultAnswer, redémarrer l’App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer une base de connaissances](../How-to/manage-knowledge-bases.md)