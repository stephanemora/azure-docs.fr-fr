---
title: Collaboration sur la base de connaissances - QnA Maker
description: QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. Cette fonctionnalité est fournie avec le contrôle d’accès en fonction du rôle Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 4c550a36dd045873df4bc4a382e1c884466f95ad
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054046"
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

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Accédez à l’onglet **Contrôle d’accès (IAM)** .

    ![IAM pour QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Sélectionnez **Ajouter**.

    ![Ajoutez IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Sélectionnez un rôle dans la liste suivante :

    |Role|
    |--|
    |Propriétaire|
    |Contributeur|
    |Lecteur QnA Maker Cognitive Services|
    |Éditeur QnA Maker Cognitive Services|
    |Utilisateur Cognitive Services|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="IAM pour QnA Maker, ajouter un rôle.":::

1. Saisissez l’adresse e-mail de l’utilisateur et cliquez sur **Enregistrer**.

    ![Ajoutez une adresse e-mail IAM à QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Afficher les bases de connaissances QnA Maker

Lorsque la personne avec laquelle vous avez partagé votre service QnA Maker se connecte au [portail QnA Maker](https://qnamaker.ai), elle peut voir toutes les bases de connaissances de ce service en fonction de leur rôle.

Lorsqu’elle sélectionne une base de connaissances, son rôle actuel sur cette ressource QnA Maker est visible à côté du nom de la base de connaissances.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Capture d’écran de la base de connaissances en mode d’édition avec un nom de rôle entre parenthèses à côté du nom de la base de connaissances dans l’angle supérieur gauche de la page web.":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tester une base de connaissances](./test-knowledge-base.md)

En savoir plus sur la collaboration :
* Contrôle d’accès en fonction du rôle [Azure](../../../active-directory/role-based-access-control-configure.md)
* [Concepts](../Concepts/role-based-access-control.md) de contrôle d’accès en fonction du rôle QnA Maker
