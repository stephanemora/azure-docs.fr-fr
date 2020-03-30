---
title: Collaboration sur la base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le contrôle d’accès en fonction du rôle Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660747"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborer sur votre base de connaissances

QnA Maker permet aux utilisateurs de collaborer sur toutes les bases de connaissances dans la même ressource QnA Maker. Cette fonctionnalité est fournie avec le [contrôle d'accès en fonction du rôle](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) Azure.

Effectuez les étapes suivantes pour partager votre service QnA Maker avec une quelqu’un :

1. Connectez-vous au portail Microsoft Azure et accédez à votre ressource QnA Maker.

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Accédez à l’onglet **Contrôle d’accès (IAM)** .

    ![IAM pour QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Sélectionnez **Ajouter**.

    ![Ajoutez IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Sélectionnez le rôle de **propriétaire** ou de **contributeur**. Vous ne pouvez pas accorder un accès en lecture seule via le contrôle d’accès en fonction du rôle. Les rôles propriétaire et contributeur disposent d’autorisations d’accès en lecture-écriture au service QnA Maker.

    ![Ajoutez un rôle IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Saisissez l’adresse e-mail de l’utilisateur et cliquez sur **Enregistrer**.

    ![Ajoutez une adresse e-mail IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Lorsque la personne avec laquelle vous avez partagé votre service QnA Maker se connecte au [portail QnA Maker](https://qnamaker.ai), elle peut voir toutes les bases de connaissances de ce service.

N’oubliez pas que vous ne pouvez pas partager une base de connaissances particulière dans un service QnA Maker. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différents services QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tester une base de connaissances](./test-knowledge-base.md)
