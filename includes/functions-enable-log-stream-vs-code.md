---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010500"
---
Pour activer les journaux de diffusion en continu pour votre Function App dans Azure :

1. Sélectionnez F1 pour ouvrir la palette de commandes, puis recherchez et exécutez la commande **Azure Functions : Démarrer le streaming des journaux**.

1. Sélectionnez votre Function App dans Azure, puis sélectionnez **Oui** pour activer la journalisation d’application de votre Function App.

1. Déclenchez vos fonctions dans Azure. Notez que les données de journal sont affichées dans la fenêtre de résultats de Visual Studio Code.

1. Quand vous avez terminé, n’oubliez pas d’exécuter la commande **Azure Functions : Arrêter le streaming des journaux** pour désactiver la journalisation pour l’application de fonction.