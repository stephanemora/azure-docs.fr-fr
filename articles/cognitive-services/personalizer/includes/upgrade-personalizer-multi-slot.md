---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: ce9bc2807708375847594674e5c28330cbe7c23f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110486940"
---
### <a name="upgrade-personalizer-instance-to-multislot"></a>Mettre à niveau une instance Personalizer sur plusieurs emplacements

> [!NOTE]
> La personnalisation sur plusieurs emplacements (préversion) a un impact sur les autres fonctionnalités du service Personalizer. Cette modification est irréversible. Avant d’activer la personnalisation sur plusieurs emplacements, consultez [Personnalisation sur plusieurs emplacements (préversion)](../concept-multi-slot-personalization.md). 

Dans le portail Azure, accédez à la ressource Personalizer de la page **Paramètres de modèle et d’apprentissage** et cliquez sur **Exporter les paramètres d’apprentissage**. Le champ **arguments** du fichier json téléchargé commencera par **--cb_explore_adf**. Remplacez cette valeur par **--ccb_explore_adf** tout en laissant le reste du fichier inchangé. Enregistrez le fichier . 

![Paramètres d’apprentissage avant modification](../media/settings/learning-settings-pre-upgrade.png)

![Paramètres d’apprentissage après modification](../media/settings/learning-settings-post-upgrade.png)

Dans le même onglet sur le portail, sous **Importer les paramètres d’apprentissage**, recherchez votre fichier json récemment modifié et chargez-le. Votre instance Personalizer est ainsi mise à jour en Personalizer sur « plusieurs emplacements », elle prend désormais en charge les appels de classement et de récompense sur plusieurs emplacements.
