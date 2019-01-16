---
title: Collaboration sur la base de connaissances - Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le contrôle d’accès en fonction du rôle Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/08/2018
ms.author: tulasim
ms.openlocfilehash: ec26e7ab535084fa5d8f52c11c671212eeab0f13
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119830"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborer sur votre base de connaissances

QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le [contrôle d'accès en fonction du rôle](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) Azure. 

Effectuez les étapes suivantes pour partager votre service QnA Maker avec une quelqu’un :

1. Connectez-vous au portail Azure et accédez à votre ressource QnA Maker.

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Accédez à l’onglet **Contrôle d’accès (IAM)**.

    ![IAM pour QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Sélectionnez **Ajouter**.

    ![Ajoutez IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Sélectionnez le rôle de **propriétaire** ou de **contributeur**. Vous ne pouvez pas accorder un accès en lecture seule via le contrôle d’accès en fonction du rôle. Les rôles Propriétaire et Contributeur disposent d'un accès en lecture-écriture au service QnA Maker.

    ![Ajoutez un rôle IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Entrez l’adresse e-mail avec laquelle vous souhaitez partager et appuyez sur Enregistrer.

    ![Ajoutez une adresse e-mail IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Maintenant, lorsque la personne avec laquelle vous avez partagé votre service QnA Maker se connecte au [portail QnA Maker](https://qnamaker.ai), elle peut voir toutes les bases de connaissances de ce service.

N’oubliez pas que vous ne pouvez pas partager une base de connaissances particulière dans un service QnA Maker. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différents services QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tester une base de connaissances](./test-knowledge-base.md)
