---
title: Configurer les paramètres
titleSuffix: Azure Cognitive Services
description: La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: bce260d2a6b897d3e45b231df4eecd7ecb31ba31
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143258"
---
# <a name="personalizer-settings"></a>Paramètres de Personalizer

La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.

## <a name="create-personalizer-resource"></a>Créer une ressource Personalizer

Créez une ressource Personalizer pour chaque boucle de rétroaction. 

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Le lien précédent vous amène vers la page **Créer** du service Personalizer. 
1. Entrez le nom de votre service, puis sélectionnez un abonnement, un emplacement, un niveau tarifaire et un groupe de ressources.
1. Sélectionnez la confirmation, puis **Créer**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Configurer les paramètres du service dans le portail Azure

1. Connectez-vous au [Portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Recherchez votre ressource Personalizer. 
1. Dans la section **Gestion des ressources**, sélectionnez **Paramètres**.

    Avant de quitter le portail Azure, copiez l’une de vos clés de ressource à partir de la page **Clés**. Vous en aurez besoin pour utiliser le [SDK Personalizer](https://aka.ms/personalizer-dotnet-sdk-docs).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Configurer les paramètres de récompense pour la boucle de rétroaction en fonction du cas d’usage

Configurez les paramètres du service pour l’utilisation des récompenses par votre boucle de rétroaction. Les modifications apportées aux paramètres suivants réinitialisent le modèle actuel de Personalizer et le réentraînent avec les 2 derniers jours de données :

![Configurer les paramètres de récompense pour la boucle de rétroaction](media/settings/configure-model-reward-settings.png)

|Paramètre|Objectif|
|--|--|
|Temps d’attente des récompenses|Définit la durée pendant laquelle Personalizer collecte les valeurs des récompenses pour un appel du classement, en commençant au moment où l’appel du classement se produit. Vous définissez cette valeur en vous posant cette question : « Combien de temps Personalizer doit-il attendre les appels des récompenses ? » Toute récompense arrivant après cette fenêtre de temps est journalisée, mais elle n’est pas utilisée pour l’apprentissage.|
|Récompense par défaut|Si aucun appel de récompense n’est reçu par Personalizer pendant la fenêtre Temps d’attente des récompenses associée à un appel du classement, Personalizer affecte la récompense par défaut. Par défaut, et dans la plupart des scénarios, la récompense par défaut est égale à zéro.|
|Agrégation de récompenses|Si plusieurs récompenses sont reçues pour le même appel de l’API de classement, cette méthode d’agrégation est utilisée : **somme** ou **le plus ancien**. « Le plus ancien » sélectionne le score reçu le plus ancien et ignore le reste. C’est utile si vous voulez une seule récompense entre des appels éventuellement en double. |

Après avoir changé ces paramètres, veillez à sélectionner **Enregistrer**.

### <a name="exploration-setting"></a>Paramètre Exploration 

La personnalisation est en mesure de découvrir de nouveaux modèles et de s’adapter aux changements de comportement des utilisateurs au fil du temps en explorant des alternatives. Le paramètre **Exploration** détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration. 

Les modifications apportées à ce paramètre réinitialisent le modèle actuel de Personalizer et le réentraînent avec les 2 derniers jours de données.

![Le paramètre Exploration détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration](media/settings/configure-exploration-setting.png)

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

### <a name="model-update-frequency"></a>Fréquence de mise à jour du modèle

**Fréquence de mise à jour du modèle** définit la fréquence à laquelle un nouveau modèle Personalizer est réentraîné. 

![La fréquence de mise à jour du modèle définit la fréquence à laquelle un nouveau modèle Personalizer est réentraîné.](media/settings/configure-model-update-frequency-settings.png)

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

### <a name="data-retention"></a>Conservation des données

**Période de conservation des données** définit le nombre de jours que Personalizer conserve les journaux de données. Les journaux de données du passé sont nécessaires pour effectuer des [évaluations hors connexion](concepts-offline-evaluation.md), qui sont utilisées pour mesurer l’efficacité de Personalizer et pour optimiser la stratégie d’apprentissage.

Après avoir changé ce paramètre, veillez à sélectionner **Enregistrer**.

## <a name="export-the-personalizer-model"></a>Exporter le modèle de Personalizer

Dans la section Gestion des ressources pour **Modèle et stratégie**, passez en revue la création du modèle et la date de la dernière mise à jour, puis exportez le modèle actuel.

![Exporter le modèle actuel de Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importer et exporter la stratégie d’apprentissage

Dans la section Gestion des ressources pour **Modèle et stratégie**, importez une nouvelle stratégie d’apprentissage ou exportez la stratégie d’apprentissage actuelle.

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour utiliser le conteneur Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
