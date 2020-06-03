---
title: Collaboration sur la base de connaissances - QnA Maker
description: QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le contrôle d’accès en fonction du rôle Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650788"
---
# <a name="collaboration-with-authors-and-editors"></a>Collaboration avec les auteurs et les éditeurs

La collaboration est fournie au niveau de la ressource QnA Maker pour vous permettre de limiter l’accès des collaborateurs en fonction du rôle du collaborateur. En savoir plus sur les [concepts](../Concepts/role-based-access-control.md) d’authentification des collaborateurs QnA Maker.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Ajouter un accès en fonction du rôle (contrôle d’accès en fonction du rôle) à votre ressource QnA Maker

QnA Maker permet aux utilisateurs de collaborer sur toutes les bases de connaissances dans la même ressource QnA Maker. Cette fonctionnalité est fournie avec le [contrôle d'accès en fonction du rôle](../../../active-directory/role-based-access-control-configure.md) Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Accès au niveau de la ressource QnA Maker

Vous ne pouvez pas partager une base de connaissances particulière dans un service QnA Maker. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différentes ressources QnA Maker, puis ajoutez des rôles à chaque ressource.

## <a name="add-role-to-resource"></a>Ajouter un rôle à la ressource

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Ajouter un compte d’utilisateur à la ressource QnA Maker

Les étapes suivantes utilisent le rôle de collaborateur, cependant tous les [rôles](../reference-role-based-access-control.md) peuvent être ajoutés à l’aide de ces étapes

1. Connectez-vous au portail [Microsoft Azure](https://portal.azure.com/) et accédez à votre ressource QnA Maker.

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Accédez à l’onglet **Contrôle d’accès (IAM)** .

    ![IAM pour QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Sélectionnez **Ajouter**.

    ![Ajoutez IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Sélectionnez un rôle dans la liste suivante :

    |Role|
    |--|
    |Propriétaire|
    |Contributeur|
    |Lecteur QnA Maker|
    |Éditeur QnA Maker|
    |Utilisateur Cognitive Services|

    ![Ajoutez un rôle IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Saisissez l’adresse e-mail de l’utilisateur et cliquez sur **Enregistrer**.

    ![Ajoutez une adresse e-mail IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Lorsque la personne avec laquelle vous avez partagé votre service QnA Maker se connecte au [portail QnA Maker](https://qnamaker.ai), elle peut voir toutes les bases de connaissances de ce service en fonction de leur rôle.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tester une base de connaissances](./test-knowledge-base.md)

En savoir plus sur la collaboration :
* Contrôle d’accès en fonction du rôle [Azure](../../../active-directory/role-based-access-control-configure.md)
* [Concepts](../Concepts/role-based-access-control.md) de contrôle d’accès en fonction du rôle QnA Maker
