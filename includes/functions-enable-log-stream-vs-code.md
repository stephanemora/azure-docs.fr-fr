---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 83226fb98f28d95d6244c93043e95424d2da7fe6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455423"
---
Pour activer les journaux de diffusion en continu pour votre Function App dans Azure :

1. Appuyez sur F1 pour ouvrir la palette de commandes, puis recherchez et exécutez la commande **Azure Functions : Start Streaming Logs** (Démarrer les journaux de diffusion en continu).

1. Sélectionnez votre Function App dans Azure, puis sélectionnez **Oui** pour activer la journalisation d’application de votre Function App.

1. Déclenchez vos fonctions dans Azure et notez que les données de journal sont affichées dans la fenêtre de résultats de Visual Studio Code.

1. Lorsque vous avez terminé, n’oubliez pas d’exécuter la commande **Azure Functions : Stop Streaming Logs** (Arrêter les journaux de diffusion en continu) pour désactiver la journalisation de la Function App.