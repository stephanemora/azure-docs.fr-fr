---
title: Fichier include
description: Fichier include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807522"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Créer une rubrique à l’aide du Portail Azure
1. Dans la page **Espace de noms Service Bus**, sélectionnez **Rubriques** dans le menu de gauche.
2. Sélectionnez **+ Rubrique** dans la barre d’outils. 
4. Entrez un **nom** pour la rubrique. Conservez les valeurs par défaut des autres options.
5. Sélectionnez **Create** (Créer).

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="Image montrant la page Créer une rubrique.":::

## <a name="create-a-subscription-to-the-topic"></a>Créer un abonnement à la rubrique
1. Sélectionnez la **rubrique** que vous avez créée dans la section précédente. 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="Image montrant la sélection de la rubrique dans la liste des rubriques.":::
2. Dans la page **Rubrique Service Bus**, dans la barre d’outils, sélectionnez **+ Abonnement**. 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="Image montrant le bouton Ajouter un abonnement.":::    
3. Dans la page **Créer un abonnement**, procédez comme suit :
    1. Entrez **S1** pour le **nom** de l’abonnement.
    1. Entrez **3** pour **Nombre maximal de remises**.
    1. Ensuite, sélectionnez **Créer** pour créer l’abonnement. 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="Image montrant la page Créer un abonnement.":::
