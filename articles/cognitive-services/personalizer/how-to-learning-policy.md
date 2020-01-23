---
title: Gérer la stratégie d’apprentissage - Personalizer
description: Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75840715"
---
# <a name="manage-learning-policy"></a>Gérer la stratégie d’apprentissage

Les paramètres de stratégie d’apprentissage déterminent les _hyperparamètres_ de l’entraînement des modèles. La stratégie d’apprentissage est définie dans un fichier `.json`.

## <a name="import-a-new-learning-policy"></a>Importer une nouvelle stratégie d’apprentissage

1. Ouvrez le [portail Azure](https://portal.azure.com) et sélectionnez votre ressource Personalizer.
1. Sélectionnez **Paramètres de modèle et d’apprentissage**  dans la section **Gestion des ressources**.
1. Pour **Importer les paramètres d’apprentissage** sélectionnez le fichier que vous avez créé au format JSON spécifié ci-dessus, puis sélectionnez le bouton **Charger**.

    Attendez la notification qui indique que la stratégie d’apprentissage a été chargée avec succès.

## <a name="export-a-learning-policy"></a>Exporter une stratégie d’apprentissage

1. Ouvrez le [portail Azure](https://portal.azure.com) et sélectionnez votre ressource Personalizer.
1. Sélectionnez **Paramètres de modèle et d’apprentissage**  dans la section **Gestion des ressources**.
1. Pour **Importer les paramètres d’apprentissage**, sélectionnez le bouton **Exporter les paramètres d’apprentissage**. Cette opération enregistre le fichier `json` sur votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes

Découvrir les [concepts](concept-active-learning.md#learning-settings) de la stratégie d’apprentissage

[Découvrir la disponibilité dans la région](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)