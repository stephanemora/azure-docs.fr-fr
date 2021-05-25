---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7f5f062e35aad6b7623f1a2f97ab3b9133266ac6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748268"
---
Affectez les attributions de rôle App Configuration appropriées aux informations d’identification utilisées dans la tâche, afin que la tâche puisse accéder au magasin App Configuration.

1. Accédez à votre magasin App Configuration cible. 
1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Dans le volet droit, sélectionnez **Ajouter des attributions de rôles**.
    
    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment-button.png" alt-text="Capture d’écran montrant le bouton Ajouter des attributions de rôles.":::

1. Pour **Rôle**, sélectionnez **Propriétaire des données App Configuration**. Ce rôle permet à la tâche de lire et d’écrire dans le magasin App Configuration. 
1. Sélectionnez le principal de service associé à la connexion de service que vous avez créée dans la section précédente.

    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment.png" alt-text="Capture d’écran montrant la boîte de dialogue Ajouter une attribution de rôle.":::
