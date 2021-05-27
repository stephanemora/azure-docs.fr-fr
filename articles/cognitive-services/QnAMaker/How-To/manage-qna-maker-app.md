---
title: Gérer une application QnA Maker - QnA Maker
description: QnA Maker permet aux utilisateurs de collaborer sur une base de connaissances. QnA Maker vous donne la possibilité d'améliorer la qualité de votre base de connaissances grâce à l'apprentissage actif. Examinez, acceptez ou refusez, ajoutez sans supprimer ou modifier les questions existantes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: fc5de9ff694c1c5a3841c206334fb87d240a568c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378768"
---
# <a name="manage-qna-maker-app"></a>Gérer une application QnA Maker

QnA Maker vous permet de collaborer avec différents auteurs et éditeurs de contenu en vous donnant la possibilité de limiter l'accès des collaborateurs en fonction de leur rôle.
Découvrez-en plus sur les [concepts d'authentification des collaborateurs QnA Maker](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Ajouter le Contrôle d'accès en fonction du rôle Azure (Azure RBAC)

QnA Maker permet aux utilisateurs de collaborer sur toutes les bases de connaissances dans la même ressource QnA Maker. Cette fonctionnalité est fournie avec le [Contrôle d'accès en fonction du rôle Azure (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-cognitive-resource-level"></a>Accès au niveau de la ressource cognitive

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Vous ne pouvez pas partager une base de connaissances particulière dans un service QnA Maker. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différentes ressources QnA Maker, puis ajoutez des rôles à chaque ressource.

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (en préversion)](#tab/v2)

Vous ne pouvez pas partager une base de connaissances particulière dans un service Analyse de texte. Si vous souhaitez que le contrôle d’accès soit plus granulaire, envisagez de répartir vos bases de connaissances sur différentes ressources Analyse de texte, puis ajoutez des rôles à chaque ressource.

---

## <a name="add-a-role-to-a-resource"></a>Ajouter un rôle à une ressource

### <a name="add-a-user-account-to-the-cognitive-resource"></a>Ajouter un compte d’utilisateur à la ressource cognitive

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Nous vous conseillons d’appliquer les contrôles RBAC à la ressource QnA Maker.

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (en préversion)](#tab/v2)

Nous vous conseillons d’appliquer les contrôles RBAC à la ressource Analyse de texte avec Réponses aux questions personnalisées en tant que fonctionnalité.

---

Les étapes suivantes utilisent le rôle de collaborateur, cependant tous les rôles peuvent être ajoutés à l’aide de ces étapes

1. Connectez-vous au portail [Azure](https://portal.azure.com/) et accédez à votre ressource cognitive.

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
> [Créer une base de connaissances](./manage-knowledge-bases.md)
