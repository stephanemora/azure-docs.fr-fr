---
title: Se connecter à d'autres services tout en modérant du contenu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Découvrez comment accéder à d’autres API pour vos flux de travail Content Moderator à l’aide de connecteurs.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3ee582e2541e4eb55e5ea1424782df132ecf3575
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116552"
---
# <a name="connect-to-other-cognitive-services"></a>Se connecter à d'autres services Cognitive Services

Les flux de travail Azure Content Moderator peuvent utiliser d’autres API en plus des API de Content Moderator. Vous pouvez accéder à d’autres API à l’aide d’un connecteur de Content Moderator. Le connecteur fournit un lien vers les autres API.

Content Moderator inclut ces connecteurs par défaut :

* API Émotion
* API Visage
* Service cloud PhotoDNA

![Connecteurs disponibles dans Content Moderator](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Vérifier vos informations d’identification 

Avant de définir un flux de travail, assurez-vous que vous disposez des informations d’identification valides pour l’API du connecteur que vous souhaitez utiliser :

1. Dans le tableau de bord de l’outil de révision, sélectionnez **Paramètres** > **Connecteurs**.

   ![Sélection de Connecteurs dans Content Moderator](images/connectors-2.png)

2. Sélectionnez le symbole **Modifier** en regard du connecteur pour lequel vous souhaitez vérifier les informations d’identification.

   ![Sélection du symbole Modifier dans Content Moderator](images/connectors-3.png)

3. La clé de l’abonnement s’affiche. Si vous avez apporté des modifications, sélectionnez **Enregistrer** lorsque vous avez terminé.

   ![Page Modifier le connecteur de Content Moderator](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Ajouter un connecteur

1. Avant d’ajouter un connecteur, vous avez besoin d’une clé d’abonnement. Dans le tableau de bord de l’outil de révision, sélectionnez **Paramètres** > **Informations d’identification**. Sélectionnez et copiez la valeur qui se trouve dans la zone **Ocp-Admin-Subscription-Key**.

2. Sélectionnez **Connecteurs**. Sélectionnez l’un des connecteurs disponibles qui sont affichés dans le tableau de bord de l’outil de révision, puis **Connecter**. 

   ![Page Ajouter un connecteur de Content Moderator](images/connectors-5.png)

3. Dans la zone **Ocp-Admin-Subscription-Key**, collez la clé que vous avez copiée. Sélectionnez ensuite **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment utiliser des connecteurs pour [définir des flux de travail personnalisés](workflows.md).
