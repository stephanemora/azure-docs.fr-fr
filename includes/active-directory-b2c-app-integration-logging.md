---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 991883785974648597141f0333679b39477a6ff9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524048"
---
## <a name="configure-logging"></a>Configuration de la journalisation

La bibliothèque MSAL génère des messages de journal qui peuvent aider à diagnostiquer les problèmes. L’application peut configurer la journalisation, contrôler de façon personnalisée le niveau de détail et savoir si des données personnelles et d’organisation sont consignées ou non. 

Nous vous conseillons de créer un rappel de journalisation et de donner aux utilisateurs le moyen d’envoyer des journaux lorsqu’ils rencontrent des problèmes d’authentification. MSAL fournit plusieurs niveaux de détail de journalisation :

- Erreur : indique qu’un problème est survenu et qu’une erreur a été générée. Utilisé pour le débogage et l’identification des problèmes.
- Avertissement : Il n’y a pas nécessairement eu une erreur ou une défaillance, mais l’objectif est d’effectuer un diagnostic et de mettre en avant des problèmes éventuels.
- Information : MSAL enregistrera des événements destinés à fournir des informations et pas nécessairement destinés à être débogués.
- Verbose : Par défaut. MSAL enregistre les détails complets du comportement de la bibliothèque.

Par défaut, l’enregistreur d’événements MSAL ne capture aucune donnée personnelle ou d’organisation. La bibliothèque permet d’activer la journalisation de données personnelles et d’organisation si vous décidez de le faire.



