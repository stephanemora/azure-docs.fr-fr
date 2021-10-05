---
title: fichier descriptif
description: Fichier include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3a5413591ba637364bbb42a7e7aedf77b40fb898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565680"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Créer une rubrique à l’aide du Portail Azure
1. Dans la page **Espace de noms Service Bus**, sélectionnez **Rubriques** dans le menu de gauche.
2. Sélectionnez **+ Rubrique** dans la barre d’outils. 
4. Entrez un **nom** pour la rubrique. Conservez les valeurs par défaut des autres options.
5. Sélectionnez **Create** (Créer).

    ![Créer une rubrique](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Créer des abonnements à la rubrique
1. Sélectionnez la **rubrique** que vous avez créée dans la section précédente. 
    
    ![Sélectionner la rubrique](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Dans la page **Rubrique Service Bus**, sélectionnez **Abonnements** dans le menu de gauche, puis **+ Abonnement** dans la barre d’outils. 
    
    ![Bouton Ajouter un abonnement](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Dans la page **Créer un abonnement**, procédez comme suit :
    1. Entrez **S1** pour le **nom** de l’abonnement.
    1. Entrez **3** pour **Nombre maximal de remises**.
    1. Ensuite, sélectionnez **Créer** pour créer l’abonnement. 

        ![Page Créer un abonnement](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Répétez la dernière étape deux autre fois pour créer des abonnements nommés **S2** et **S3**.