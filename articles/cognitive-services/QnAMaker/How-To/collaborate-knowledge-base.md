---
title: Collaboration sur votre base de connaissances - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Comment collaborer sur votre base de connaissances QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369556"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborer sur votre base de connaissances

QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le [contrôle d'accès en fonction du rôle](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) Azure. 

Effectuez les étapes suivantes pour partager votre service QnA Maker avec une quelqu’un :

1. Connectez-vous au portail Azure et accédez à votre ressource QnA Maker.

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Accédez à l’onglet **Contrôle d’accès (IAM)**.

    ![IAM pour QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Sélectionnez **Ajouter**.

    ![Ajoutez IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Sélectionnez le rôle de **propriétaire** ou de **contributeur**.

    ![Ajoutez un rôle IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Entrez l’adresse e-mail avec laquelle vous souhaitez partager et appuyez sur Enregistrer.

    ![Ajoutez une adresse e-mail IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Maintenant, lorsque la personne avec laquelle vous avez partagé votre service QnA Maker se connecte au [portail QnA Maker](https://qnamaker.ai), elle peut voir toutes les bases de connaissances de ce service.

N’oubliez pas que vous ne pouvez pas partager une base de connaissances particulière dans un service QnA Maker. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différents services QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tester une base de connaissances](./test-knowledge-base.md)
