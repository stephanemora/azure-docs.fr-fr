---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763238"
---
Les applications de la Bibliothèque d’authentification Microsoft (MSAL) génèrent des messages de journal qui contribuent à diagnostiquer des problèmes. Une application peut configurer la journalisation avec quelques lignes de code et contrôler de façon personnalisée le niveau de détail, ainsi que permettre de savoir si des données personnelles et d’organisation sont consignées ou non. Nous vous conseillons de créer un rappel de journalisation et de donner aux utilisateurs le moyen d’envoyer des journaux lorsqu’ils rencontrent des problèmes d’authentification.

## <a name="logging-levels"></a>Niveaux de journalisation

MSAL fournit plusieurs niveaux de détail de journalisation :

- Erreur : indique qu’un problème est survenu et qu’une erreur a été générée. Utilisé pour le débogage et l’identification des problèmes.
- Avertissement : Il n’y a pas nécessairement eu une erreur ou une défaillance, mais l’objectif est d’effectuer un diagnostic et de mettre en avant des problèmes éventuels.
- Information : MSAL enregistrera des événements destinés à fournir des informations et pas nécessairement destinés à être débogués.
- Verbose : Par défaut. MSAL enregistre les détails complets du comportement de la bibliothèque.

## <a name="personal-and-organizational-data"></a>Données personnelles et d’organisation

Par défaut, l’enregistreur d’événements MSAL ne capture pas de données personnelles ou d’organisation hautement sensibles. La bibliothèque permet d’activer la journalisation de données personnelles et d’organisation si vous décidez de le faire.

Les sections suivantes fournissent plus de détails sur la journalisation des erreurs MSAL pour votre application.