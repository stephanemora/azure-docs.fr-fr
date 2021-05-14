---
title: Fichier include
description: Fichier include
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 04/23/2021
ms.topic: include
ms.openlocfilehash: 727178c2851e43c787c2b1fcf1e7bb349b3ea4f5
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107948335"
---
## <a name="sign-in-to-luis-portal"></a>Se connecter au portail LUIS

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

Un nouvel utilisateur de LUIS doit suivre cette procédure :

1. Connectez-vous au [portail LUIS](https://www.luis.ai), sélectionnez votre pays/région, puis acceptez les conditions d’utilisation. Si vous voyez à la place **Mes applications**, c’est qu’une ressource LUIS existe déjà et vous devez passer directement à la création d’une application. Sinon, commencez par utiliser une ressource Azure. Ceci vous permet de lier votre compte LUIS à une ressource de création Azure nouvelle ou existante. <!---This is equivalent to signing up already migrated. You won't need to go through the [migration process](../luis-migration-authoring.md#what-is-migration) later on.-->
<!---
    * Using a trial key. This allows you to sign in to LUIS with a trial resource that you don't need to set up. If you choose this option, you will eventually be required to [migrate your account](../luis-migration-authoring.md#migration-steps) and link your applications to an authoring resource.
-->

2. Dans la fenêtre **Choisir une ressource de création** qui s’affiche, recherchez votre abonnement Azure et la ressource de création LUIS. Si vous n’avez pas de ressource, vous pouvez en créer une nouvelle.

    <!---:::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Choose a type of Language Understanding authoring resource.":::
    -->
    ![Choisissez un type de ressource de création LUIS.](../media/luis-how-to-azure-subscription/choose-authoring-resource.png)
    
    Quand vous créez une ressource de création, fournissez les informations suivantes :
    * **Nom du locataire** – locataire auquel votre abonnement Azure est associé.
    * **Nom de l’abonnement Azure** – abonnement auquel la ressource sera facturée.
    * **Nom du groupe de ressources Azure** – nom de groupe de ressources personnalisé que vous choisissez ou créez. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.
    * **Nom de la ressource Azure** – nom personnalisé que vous choisissez, utilisé comme élément de l’URL pour vos requêtes de point de terminaison de création et de prédiction.
    * **Pricing tier** (Niveau tarifaire) : le niveau tarifaire détermine la transaction maximale par seconde et par mois.


