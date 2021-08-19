---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: 0f181a28d1522fb433a274361c96e532c6a0b708
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255920"
---
### <a name="upgrade-personalizer-instance-to-multi-slot"></a>Mettre à niveau une instance Personalizer sur plusieurs emplacements

> [!NOTE]
> La personnalisation sur plusieurs emplacements (préversion) a un impact sur les autres fonctionnalités du service Personalizer. Cette modification est irréversible. Avant d’activer la personnalisation sur plusieurs emplacements, consultez [Personnalisation sur plusieurs emplacements (préversion)](../concept-multi-slot-personalization.md). 


1. Désactivez l’optimisation automatique dans le portail Azure : dans la ressource Personalizer, sous **Gestion des ressources**, sur la page **Paramètres de modèle et d’apprentissage**, désactivez l’optimisation automatique et enregistrez.

> [!NOTE]
> La personnalisation sur plusieurs emplacements ne fonctionne pas si vous ne désactivez pas l’optimisation automatique. L’optimisation automatique pour la personnalisation sur plusieurs emplacements sera prise en charge à l’avenir. 

2. Mettez à jour Personalizer sur plusieurs emplacements dans le portail Azure : dans la ressource Personalizer, sous **Gestion des ressources**, sur la page **Paramètres de modèle et d’apprentissage**, cliquez sur **Exporter les paramètres d’apprentissage**. Le champ **arguments** du fichier json téléchargé commencera par **--cb_explore_adf**. Remplacez cette valeur par **--ccb_explore_adf** et enregistrez le fichier. CB (Contextual Bandits) et CCB (Conditional Contextual Bandits) sont les algorithmes que Personalizer utilise pour la personnalisation à un seul emplacement et sur plusieurs emplacements, respectivement. Les ADF (fonctionnalités dépendantes d’action) signifient que les actions sont exprimées/identifiées avec des fonctionnalités.

![Paramètres d’apprentissage avant modification](../media/settings/learning-settings-pre-upgrade.png)

![Paramètres d’apprentissage après modification](../media/settings/learning-settings-post-upgrade.png)

Dans le même onglet sur le portail, sous **Importer les paramètres d’apprentissage**, recherchez votre fichier json récemment modifié et chargez-le. Votre instance Personalizer est ainsi mise à jour en Personalizer sur « plusieurs emplacements », elle prend désormais en charge les appels de classement et de récompense sur plusieurs emplacements.


