---
title: Configurer Personalizer
titleSuffix: Azure Cognitive Services
description: La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833904"
---
# <a name="configure-personalizer"></a>Configurer Personalizer

La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.

## <a name="create-personalizer-resource"></a>Créer une ressource Personalizer

Créez une ressource Personalizer pour chaque boucle de rétroaction.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Le lien précédent vous permet d'accéder à la page **Créer** du service Personalizer.
1. Entrez le nom de votre service, puis sélectionnez un abonnement, un emplacement, un niveau tarifaire et un groupe de ressources.
1. Sélectionnez la confirmation, puis **Créer**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Configurer un service dans le portail Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Recherchez votre ressource Personalizer.
1. Dans la section **Gestion des ressources**, sélectionnez **Configuration**.

    Avant de quitter le portail Azure, copiez l’une de vos clés de ressource à partir de la page **Clés**. Vous en aurez besoin pour utiliser le [SDK Personalizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Configurer une récompense pour la boucle de rétroaction en fonction du cas d’usage

Configurez le service pour l’utilisation des récompenses par votre boucle de rétroaction. Les modifications apportées aux valeurs suivantes réinitialisent le modèle de Personalizer actuel, et effectuent de nouveau son apprentissage avec les données des 2 derniers jours :

![Configurer les valeurs de récompense pour la boucle de rétroaction](media/settings/configure-model-reward-settings.png)

|Valeur|Objectif|
|--|--|
|Temps d’attente des récompenses|Définit la durée pendant laquelle Personalizer collecte les valeurs des récompenses pour un appel du classement, en commençant au moment où l’appel du classement se produit. Vous définissez cette valeur en vous posant cette question : « Combien de temps Personalizer doit-il attendre les appels des récompenses ? » Toute récompense arrivant après cette fenêtre de temps est journalisée, mais elle n’est pas utilisée pour l’apprentissage.|
|Récompense par défaut|Si aucun appel de récompense n’est reçu par Personalizer pendant la fenêtre Temps d’attente des récompenses associée à un appel du classement, Personalizer affecte la récompense par défaut. Par défaut, et dans la plupart des scénarios, la récompense par défaut est égale à zéro.|
|Agrégation de récompenses|Si plusieurs récompenses sont reçues pour le même appel de l’API de classement, cette méthode d’agrégation est utilisée : **somme** ou **le plus ancien**. « Le plus ancien » sélectionne le score reçu le plus ancien et ignore le reste. C’est utile si vous voulez une seule récompense entre des appels éventuellement en double. |

Après avoir changé ces valeurs, veillez à sélectionner **Enregistrer**.

### <a name="configure-exploration"></a>Configurer l’exploration

La personnalisation est en mesure de découvrir de nouveaux modèles et de s’adapter aux changements de comportement des utilisateurs au fil du temps en explorant des alternatives. La valeur **Exploration** détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration.

Les modifications apportées à ce paramètre réinitialisent le modèle actuel de Personalizer et effectuent de nouveau son apprentissage avec les données des 2 derniers jours.

![La valeur Exploration détermine le pourcentage des appels du classement qui reçoivent une réponse donnée via une exploration](media/settings/configure-exploration-setting.png)

Après avoir changé cette valeur, veillez à sélectionner **Enregistrer**.

### <a name="model-update-frequency"></a>Fréquence de mise à jour du modèle

Le dernier modèle, qui a été entraîné à l’aide des appels d’API Reward de chaque événement actif, n’est pas utilisé automatiquement par les appels Rank de Personalizer. La **fréquence de mise à jour des modèles** définit la fréquence à laquelle le modèle utilisé par l’appel Rank est mis à jour.

Les fréquences élevées de mise à jour des modèles sont utiles lorsque vous souhaitez suivre de près les modifications apportées au comportements des utilisateurs, par exemple, dans le cas de sites d’actualités en direct, de contenu viral ou d’enchères en direct. Dans de tels scénarios, vous pouvez utiliser une fréquence de 15 minutes. Pour la plupart des cas d’usage, une fréquence de mise à jour moins élevée restera efficace. Une mise à jour effectuée toutes les minutes peut se révéler utile lorsque vous déboguez le code d’une application à l’aide de Personalizer, lorsque vous effectuez des démonstrations ou lorsque vous testez interactivement certains aspects de Machine Learning.

![La fréquence de mise à jour du modèle définit la fréquence à laquelle un nouveau modèle Personalizer est réentraîné.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Après avoir changé cette valeur, veillez à sélectionner **Enregistrer**.

### <a name="data-retention"></a>Conservation des données

**Période de conservation des données** définit le nombre de jours que Personalizer conserve les journaux de données. Les journaux de données du passé sont nécessaires pour effectuer des [évaluations hors connexion](concepts-offline-evaluation.md), qui sont utilisées pour mesurer l’efficacité de Personalizer et pour optimiser la stratégie d’apprentissage.

Après avoir changé cette valeur, veillez à sélectionner **Enregistrer**.

## <a name="export-the-personalizer-model"></a>Exporter le modèle de Personalizer

Dans la section **Paramètres de modèle et d’apprentissage** de Gestion des ressources, examinez la date de création et de dernière mise à jour du modèle, puis exportez le modèle actuel. Vous pouvez utiliser le portail Azure ou les API Personalizer pour exporter un fichier de modèle à des fins d’archivage.

![Exporter le modèle actuel de Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Effacer les données de votre boucle d'apprentissage

1. Sur le portail Azure, pour votre ressource Personalizer, dans la page **Paramètres de modèle et d’apprentissage**, sélectionnez **Effacer les données**.
1. Pour effacer toutes les données et rétablir l'état d'origine de la boucle d'apprentissage, cochez les 3 cases.

    ![Sur le portail Azure, effacez les données de la ressource Personalizer.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valeur|Objectif|
    |--|--|
    |Données de personnalisation et de récompense consignées.|Ces données de journalisation sont utilisées lors des évaluations hors connexion. Effacez les données si vous réinitialisez votre ressource.|
    |Réinitialisez le modèle Personalizer.|Ce modèle change à chaque nouvelle formation. Cette fréquence de formation est spécifiée dans la section **Fréquence du modèle de chargement** de la page **Configuration**. |
    |Définissez la stratégie d'apprentissage sur le paramètre par défaut.|Si vous avez modifié la stratégie d'apprentissage dans le cadre d'une évaluation hors connexion, la stratégie d'apprentissage d'origine est rétablie.|

1. Sélectionnez **Effacer les données sélectionnées** pour lancer le processus de suppression. L'état est signalé dans les notifications Azure, en haut à droite.

## <a name="next-steps"></a>Étapes suivantes

[Découvrir comment gérer une stratégie d’apprentissage](how-to-learning-policy.md)
