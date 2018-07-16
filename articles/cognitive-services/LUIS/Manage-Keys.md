---
title: Gérer vos clés dans LUIS | Microsoft Docs
description: Utiliser Language Understanding (LUIS) pour gérer votre API par programmation, votre point de terminaison et vos clés externes.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266020"
---
# <a name="manage-your-luis-keys"></a>Gérer vos clés LUIS
Une clé permet de créer et de publier votre application LUIS ou d’interroger votre point de terminaison. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Concepts clés
Consultez [Clés dans LUIS](luis-concept-keys.md) pour comprendre les principes de création et de clé de point de terminaison de LUIS.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Affecter une clé de point de terminaison
Sur la page **Publier une application**, il existe déjà une clé dans la table **Ressources et clés**. Il s’agit de la clé de création (démarrage). 

1. Créez une clé LUIS sur le [portail Azure](https://portal.azure.com). Pour plus d’instructions, consultez [Création d’une clé d’abonnement à l’aide d’Azure](luis-how-to-azure-subscription.md).
 
2. Pour ajouter la clé LUIS créée à l’étape précédente, cliquez sur le bouton **Ajouter une clé** afin d’ouvrir la boîte de dialogue **Affecter une clé à votre application**. 

    ![Affecter une clé à votre application](./media/luis-manage-keys/assign-key.png)
3. Sélectionnez un locataire dans la boîte de dialogue. 
 
    > [!Note]
    > Dans Azure, un locataire représente l’ID Azure Active Directory du client ou de l’organisation associés à un service. Si vous êtes déjà inscrit à un abonnement Azure avec votre compte individuel Microsoft, vous avez déjà un client ! Lorsque vous vous connectez au portail Azure, vous êtes automatiquement connecté à votre [locataire par défaut](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Vous êtes libre d'utiliser ce locataire à votre guise, mais vous avez la possibilité de créer un compte d'administrateur professionnel.

4. Choisissez l’abonnement Azure associé à la clé LUIS Azure que vous souhaitez ajouter.

5. Sélectionnez le compte LUIS Azure. La région du compte s’affiche entre parenthèses. 

    ![Choisissez la clé](./media/luis-manage-keys/assign-key-filled-out.png)

6. Après avoir affecté cette clé de point de terminaison, utilisez-la dans toutes les requêtes de point de terminaison. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Régions de publication
En savoir plus sur les[régions](luis-reference-regions.md) de publication, notamment la publication en [Europe](luis-reference-regions.md#publishing-to-europe) et en [Australie](luis-reference-regions.md#publishing-to-australia). Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication souhaitée.

## <a name="unassign-key"></a>Annuler l’affectation de la clé

* Dans la **liste de ressources et de clés**, cliquez sur l’icône Corbeille en regard de l’entité pour laquelle vous souhaitez annuler l’affectation. Ensuite, cliquez sur **OK** dans le message de confirmation pour confirmer la suppression.
 
    ![Annuler l’affectation de l’entité](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> L’annulation de la clé LUIS n’entraîne pas sa suppression de l’abonnement Azure.

## <a name="next-steps"></a>Étapes suivantes

Utilisez votre clé pour publier votre application sur la page **Publier votre application**. Pour obtenir des instructions sur la publication, consultez [Publier application](PublishApp.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website