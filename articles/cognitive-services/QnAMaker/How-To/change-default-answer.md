---
title: Obtenir la réponse par défaut - QnA Maker
description: La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être remplacer la réponse par défaut standard.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843274"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Définir la réponse par défaut pour une base de connaissances

La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être remplacer la réponse par défaut standard.

## <a name="change-default-answer"></a>Modifier la réponse par défaut

1. Accédez au [portail Azure](https://portal.azure.com) et au groupe de ressources qui représente le service QnA Maker que vous avez créé.

2. Cliquez pour ouvrir **App Service**.

    ![Sur le portail Azure, accéder à App Service pour QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Cliquez sur **Paramètres de l’application** et modifiez le champ **DefaultAnswer** pour entrer la réponse par défaut souhaitée. Cliquez sur **Enregistrer**.

    ![Sélectionner Paramètres de l’application, puis modifier DefaultAnswer pour QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Redémarrer votre App service

    ![Après avoir modifié DefaultAnswer, redémarrer l’App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un bot avec QnA Maker et LUIS](../tutorials/integrate-qnamaker-luis.md)