---
title: Obtenir des clés d’abonnement pour Custom Speech Service sur Azure | Microsoft Docs
description: Découvrez comment obtenir des clés d’abonnement pour les appels vers Custom Speech Service dans Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 84ef657af2cc3dc4a7168a815b5e51d6f4f33fd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338367"
---
# <a name="obtain-subscription-keys"></a>Obtenir des clés d’abonnement

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Pour commencer à utiliser Custom Speech Service Azure, vous devez d’abord lier votre compte d’utilisateur à un abonnement Azure. Des abonnements de niveaux gratuits et payants sont disponibles. Pour plus d’informations sur les niveaux, veuillez consulter la [page de tarification](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Obtenir une clé d’abonnement
1. Vous pouvez obtenir une clé d’abonnement à partir du portail Azure de deux manières :

    * Accédez au [portail Azure](https://ms.portal.azure.com), et ajoutez une nouvelle API Cognitive Services en recherchant _Cognitive Services_, puis en sélectionnant **API Cognitive Services**.

      ![Recherche Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Ou accédez directement aux [API Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Renseignez les champs obligatoires suivants :

      a. **Nom du compte**. Utilisez un nom qui vous convient. N’oubliez pas ce nom afin que vous puissiez trouver votre abonnement Cognitive Services dans la liste de ressources.

      b. **Abonnement**. Sélectionnez l’un de vos abonnements Azure.

      c. **Type d’API**. Sélectionnez **Custom Speech Service (préversion)**.

      d. **Emplacement**. C’est actuellement **USA Ouest**.

      e. **Niveau tarifaire**. Sélectionnez le niveau qui vous convient. **F0** est le niveau gratuit. Les quotas autorisés sont expliqués dans la [page de tarification](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Création d’un compte Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Vous devriez trouver une vue sur votre tableau de bord ou un service avec le nom de compte fourni dans votre liste de ressources. Lorsque vous la sélectionnez, une vue d’ensemble de votre service s’affiche. Dans la liste sur la gauche, sous **Gestion des ressources**, sélectionnez **Clés**. Copiez **CLÉ 1**.

      Cette clé d’abonnement est nécessaire pour les étapes suivantes.

      ![CLÉ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Ne copiez pas l’**ID d’abonnement** à partir de la page Vue d’ensemble. Vous avez besoin de la clé d’abonnement dans l’étape suivante.
      >

      ![Vue d’ensemble de l’ID d'abonnement](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Pour entrer votre clé d’abonnement, sélectionnez votre compte d’utilisateur dans le ruban, dans le coin supérieur droit. Dans le menu déroulant, sélectionnez **Abonnements**.

      ![Élément de menu Abonnements](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Une table d’abonnements apparaît ; elle est vide la première fois qu’elle est ouverte.

    ![Table d’abonnements](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Sélectionnez **Ajouter**. Entrez un nom pour l’abonnement et la clé d’abonnement. Cela peut être **CLÉ 1** (clé primaire) ou **CLÉ 2** (clé secondaire) dans votre abonnement.

      ![Nom de la clé d’abonnement](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Pour charger des données, former un modèle, ou effectuer un déploiement, vous devez lier vos activités Custom Speech Service à un abonnement Azure. Il peut s’agir d’un abonnement de niveau payant ou de niveau gratuit. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Obtenir un ID d’abonnement
Pour obtenir un ID d’abonnement, accédez au portail Azure. Recherchez *Cognitive Services* et *Custom Speech Service*, puis suivez les instructions.

> [!NOTE]
> La clé d’abonnement est requise plus loin dans ce processus.
>

## <a name="next-steps"></a>Étapes suivantes
* Commencez à créer votre [modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md).
* Commencez à créer votre [modèle de langage personnalisé](cognitive-services-custom-speech-create-language-model.md).
