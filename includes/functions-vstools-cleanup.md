---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/28/2021
ms.author: glenga
ms.openlocfilehash: ba8915bd7b45e38b54d392a2294dba31f3ed8e32
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110785199"
---
Utiliser les étapes suivantes pour supprimer l’application de fonction et ses ressources connexes afin d’éviter des coûts supplémentaires.

1. Dans Cloud Explorer, développez votre abonnement > **App Services**, cliquez avec le bouton droit sur votre application de fonction et choisissez **Ouvrir dans le portail**. 

1. Dans la page de l’application de fonction, sélectionnez l’onglet **Vue d’ensemble**, puis le lien sous **Groupe de ressources**.

   :::image type="content" source="media/functions-vstools-cleanup/functions-app-delete-resource-group.png" alt-text="Sélectionner le groupe de ressources à supprimer dans la page de l’application de fonction":::

2. Dans la page **Groupe de ressources**, passez en revue la liste des ressources incluses et vérifiez qu’elles correspondent à celles que vous souhaitez supprimer.
 
3. Sélectionnez **Supprimer le groupe de ressources** et suivez les instructions.

   Cette opération peut prendre quelques minutes. Une fois terminée, une notification s’affiche pendant quelques secondes. Vous pouvez également sélectionner l’icône représentant une cloche en haut de la page pour afficher la notification.