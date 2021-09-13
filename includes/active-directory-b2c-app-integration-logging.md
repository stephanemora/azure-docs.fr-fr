---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 720b464c02b929590df46123e0caf9a9e83fe2f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220285"
---
## <a name="configure-logging"></a>Configuration de la journalisation

La bibliothèque MSAL génère des messages de journal qui peuvent aider à diagnostiquer les problèmes. L’application peut configurer la journalisation. L’application peut également vous offrir un contrôle personnalisé sur le niveau de détail et vous permettre de déterminer si des données personnelles et organisationnelles sont journalisées. 

Nous vous conseillons de créer un rappel de journalisation MSAL et de donner aux utilisateurs le moyen d’envoyer des journaux lorsqu’ils rencontrent des problèmes d’authentification. MSAL fournit les niveaux de détail de journalisation suivants :

- **Erreur** : un problème est survenu et une erreur a été générée. Ce niveau est utilisé pour le débogage et l’identification des problèmes.
- **Avertissement** : il n’y a pas nécessairement eu une erreur ou une défaillance, mais l’information pousse à effectuer un diagnostic et met en avant d’éventuels problèmes.
- **Info** : MSAL journalise les événements qui sont destinés à des fins d’information et pas nécessairement pour le débogage.
- **Commentaires** : il s’agit du niveau par défaut. MSAL enregistre les détails complets du comportement de la bibliothèque.

Par défaut, l’enregistreur d’événements MSAL ne capture aucune donnée personnelle ou d’organisation. La bibliothèque vous offre la possibilité d’activer la journalisation des données personnelles et organisationnelles si vous décidez de le faire.
