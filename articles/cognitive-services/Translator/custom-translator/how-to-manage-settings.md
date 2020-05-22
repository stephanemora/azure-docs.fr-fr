---
title: Comment gérer les paramètres ? - Custom Translator
titleSuffix: Azure Cognitive Services
description: Guide pratique pour gérer les paramètres, créer l’espace de travail, partager l’espace de travail et gérer la clé d’abonnement dans Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: fe2c0eed2d476d01dffd22a61dd431140f26c475
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587230"
---
# <a name="how-to-manage-settings"></a>Comment gérer les paramètres

Dans la page de paramètres de Custom Translator, vous pouvez créer un espace de travail, partager votre espace de travail et ajouter ou modifier votre clé d’abonnement Microsoft Translation.

Pour accéder à la page des paramètres :

1. Se connecter au portail [Custom Translator](https://portal.customtranslator.azure.ai/).
2. Dans le portail Custom Translator, cliquez sur l’icône d’engrenage dans la barre latérale.

    ![Lien de paramétrage](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Association d’abonnement Microsoft Translator

Vous devez avoir une clé d’abonnement à Translator associée à votre espace de travail pour entrainer ou déployer des modèles.

Si vous n’avez pas d’abonnement, procédez comme suit :

1. Abonnez-vous à Translator. Cet article explique comment s’abonner à Translator.
2. Notez la clé de votre abonnement à Translator. Key1 ou Key2 sont acceptables.
3. Retournez au portail Custom Translator.

### <a name="add-existing-key"></a>Ajouter une clé existante

1.  Accédez à la page « Paramètres » de votre espace de travail.
2.  Cliquez sur Ajouter une clé

    ![Comment ajouter une clé d’abonnement](media/how-to/how-to-add-subscription-key.png)

3. Dans la boîte de dialogue, entrez la clé de votre abonnement à Translator, puis cliquez sur le bouton « Ajouter ».

    ![Comment ajouter une clé d’abonnement](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Vous pouvez modifier ou supprimer à tout moment une clé ajoutée.

    ![Après l’ajout d’une clé d'abonnement](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gérer votre espace de travail

Un espace de travail est une zone de travail pour la composition et la création de votre système de traduction personnalisé. Un espace de travail peut contenir plusieurs projets, modèles et documents.

Si différentes parties de votre travail doivent être partagées avec différentes personnes, il peut être utile de créer plusieurs espaces de travail.

## <a name="create-a-new-workspace"></a>Créer un espace de travail

1.  Accédez à la page « Paramètres » de l’espace de travail.
2.  Cliquez sur le bouton « Nouvel espace de travail » dans la section « Créer un espace de travail ».

    ![Créer un espace de travail](media/how-to/create-new-workspace.png)

4.  Dans la boîte de dialogue, entrez le nom du nouvel espace de travail.
5.  Cliquez sur « Créer ».

    ![Créer une boîte de dialogue d’espace de travail](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Partager votre espace de travail

Dans Custom Translator, vous pouvez partager votre espace de travail avec d’autres utilisateurs si certaines parties de votre travail doivent être partagées avec différentes personnes.

1.  Accédez à la page « Paramètres » de l’espace de travail.
2.  Cliquez sur le bouton « Partager » dans la section « Paramètres de partage ».

    ![Partager l’espace de travail](media/how-to/share-workspace.png)

3.  Dans la boîte de dialogue, entrez une liste séparée par des virgules des adresses e-mail avec lesquelles vous souhaitez partager cet espace de travail. Assurez-vous que vous partagez bien avec l’adresse e-mail que la personne concernée utilise pour se connecter à Custom Translator. Ensuite, sélectionnez le niveau d’autorisation de partage approprié.

4.  Si votre espace de travail a toujours le nom par défaut « Mon espace de travail », vous devrez le modifier avant de le partager.
5.  Cliquez sur « Enregistrer ».

## <a name="sharing-permissions"></a>Autorisations de partage

1.  **Lecteur :** un lecteur dans l’espace de travail sera en mesure d’afficher toutes les informations dans l’espace de travail.

2.  **Éditeur :** un éditeur dans l’espace de travail sera en mesure d’ajouter des documents, de former des modèles et de supprimer des documents et des projets. Il peut ajouter une clé d’abonnement, mais ne peut pas modifier les personnes avec lesquelles l’espace de travail est partagé, supprimer l’espace de travail ou modifier son nom.

3.  **Propriétaire :** un propriétaire dispose d’autorisations complètes pour l’espace de travail.

## <a name="change-sharing-permission"></a>Autorisation de modification du partage

Quand un espace de travail est partagé, la section « Paramètres de partage » affiche toutes les adresses e-mail avec lesquelles il est partagé. Vous pouvez modifier une autorisation de partage existante pour chaque adresse e-mail si vous avez un accès propriétaire à l’espace de travail.

1.  Dans la section « Paramètres de partage » de chaque e-mail, un menu déroulant montre le niveau d’autorisation actuel.

2.  Cliquez sur le menu déroulant et sélectionnez le nouveau niveau d’autorisation que vous souhaitez affecter à cette adresse e-mail.

    ![Paramètres d’autorisation de partage](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment migrer votre espace de travail et le projet](how-to-migrate.md) depuis [Microsoft Translator Hub](https://hub.microsofttranslator.com)
