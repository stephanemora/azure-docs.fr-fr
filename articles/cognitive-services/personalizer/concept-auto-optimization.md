---
title: Optimisation automatique – Personalizer
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité d’optimisation automatique du service Azure Personalizer.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: d2e8e73e47ebef566bed098e911b372aad6ee1e5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382548"
---
# <a name="personalizer-auto-optimize-preview"></a>Optimisation automatique de l’API Personalizer (préversion)


## <a name="introduction"></a>Introduction
L’optimisation automatique de l’API Personalizer vous évite d’avoir à maintenir manuellement une boucle de Personalizer à son meilleur niveau de performances d’apprentissage automatique, en recherchant automatiquement des paramètres d’apprentissage améliorés utilisés pour effectuer l’apprentissage de vos modèles et les appliquer. L’API Personalizer applique des critères stricts à l’utilisation de nouveaux paramètres d’apprentissage afin d’éviter que les améliorations entraînent une dégradation des résultats.

L’optimisation automatique de l’API Personalizer est en préversion publique. Ses fonctionnalités, approches et processus évolueront en fonction des commentaires des utilisateurs.

## <a name="when-to-use-auto-optimize"></a>Quand utiliser l’optimisation automatique
Dans la plupart des cas, la meilleure option est d’activer l’optimisation automatique. L’optimisation automatique est *activée* par défaut pour les nouvelles boucles de l’API Personalizer.

L’optimisation automatique peut être utile dans les situations suivantes :
* Vous créez des applications utilisées par de nombreux locataires. Chacun d’eux obtient ses propres boucles pour l’API Personalizer. C’est utile, par exemple, si vous hébergez plusieurs sites de commerce électronique. L’optimisation automatique vous évite de devoir régler manuellement des paramètres d’apprentissage pour un grand nombre de boucles de l’API Personalizer.
* Vous avez déployé l’API Personalizer et vérifié qu’elle fonctionnait correctement et produisait de bons résultats. Vous vous êtes assuré qu’elle ne générait pas de bogues ou de problèmes dans vos fonctionnalités.

> [!NOTE]
> L’optimisation automatique remplace périodiquement les paramètres d’apprentissage de l’API Personalizer. Si votre cas d’usage ou votre secteur d’activité requièrent l’audit et l’archivage des modèles et des paramètres, ou si vous avez besoin de sauvegardes de paramètres précédents, vous pouvez utiliser l’API Personalizer pour récupérer les paramètres d’apprentissage ou télécharger ceux-ci via le portail Azure.

## <a name="how-to-enable-and-disable-auto-optimize"></a>Comment activer et désactiver l’optimisation automatique
Pour activer l’optimisation automatique, utilisez le bouton bascule dans le panneau « Paramètres de modèle et d’apprentissage » sur le portail Azure. 

Vous pouvez également activer l’optimisation automatique à l’aide de l’API Personalizer `/configurations/service`.

Pour désactiver l’optimisation automatique, positionnez le bouton bascule en conséquence.

## <a name="auto-optimize-reports"></a>Rapports d’optimisation automatique

Dans le panneau Paramètres de modèle et d’apprentissage, vous pouvez voir l’historique des exécutions de l’optimisation automatique et les actions effectuées en lien avec celles-ci. 

Le tableau fournit les informations suivantes :
* moment de l’optimisation automatique ;
* fenêtre de données incluse ;
* performances des paramètres d’apprentissage en ligne, de référence et optimaux ;
* actions effectuées en lien avec la mise à jour ou non des paramètres d’apprentissage.

Les performances des différents paramètres d’apprentissage dans chaque ligne d’historique d’optimisation automatique sont affichées en chiffres absolus et en pourcentages par rapport aux performances de référence. 

**Exemple** : si le score moyen des performances de référence est de 0,20, et si le comportement de l’API Personalizer en ligne obtient un score de 0,30, les scores s’affichent respectivement sous la forme 100 % et 150 %. Si l’optimisation automatique a trouvé des paramètres d’apprentissage permettant d’atteindre un score moyen de 0,40, il s’affiche sous la forme 200 % (0,40 est égal à 200 % de 0,20). En supposant que les marges de confiance le permettent, les nouveaux paramètres sont appliqués, puis pilotent l’API Personalizer en tant que paramètres en ligne jusqu’à la prochaine exécution.

Un historique de jusqu’à 24 exécutions d’optimisation automatique antérieures est conservé pour votre analyse. Vous pouvez obtenir plus de détails sur ces évaluations et rapports hors connexion. En outre, les rapports contiennent tous les paramètres d’apprentissage figurant dans cet historique, que rechercher, télécharger ou appliquer.

## <a name="how-it-works"></a>Fonctionnement
L’API Personalizer effectue constamment l’apprentissage des modèles d’intelligence artificielle qu’elle utilise en fonction des résultats. Cet apprentissage est effectué en suivant certains *Paramètres d’apprentissage* qui contiennent des hyperparamètres et d’autres valeurs utilisées dans le processus d’apprentissage. Vous pouvez « régler » ces paramètres d’apprentissage sur votre instance spécifique de l’API Personalizer. 

L’API Personalizer a également la possibilité d’effectuer des *Évaluations hors connexion*. Des évaluations hors connexion examinent les données passées, et peuvent produire une estimation statistique des performances moyennes que les différents algorithmes et modèles de l’API Personalizer auraient pu atteindre. Pendant ce processus, l’API Personalizer recherche également de meilleurs paramètres d’apprentissage, en estimant leurs performances (résultats qu’ils auraient obtenus) au cours de cette période.

#### <a name="auto-optimize-frequency"></a>Fréquence d’optimisation automatique
L’optimisation automatique s’exécute périodiquement et opère en fonction de données passées
* Si votre application a envoyé à l’API Personalizer plus d’environ 20 Mo de données au cours des deux dernières semaines, elle utilise les deux dernières semaines de données.
* Si votre application envoie un volume de données inférieur, l’API Personalizer ajoute des données des jours précédents jusqu’à ce qu’il y ait suffisamment de données à optimiser, ou jusqu’à atteindre les données stockées les plus anciennes (à concurrence du nombre de jours de rétention des données).

Les heures et jours précis d’exécution de l’optimisation automatique sont déterminés par le service Personalizer, et fluctuent au fil du temps.

#### <a name="criteria-for-updating-learning-settings"></a>Critères de mise à jour des paramètres d’apprentissage

L’API Personalizer utilise ces estimations de résultats pour décider s’il convient de modifier les paramètres d’apprentissage actuels pour d’autres. Chaque estimation est une courbe de distribution, avec des limites de confiance supérieure et inférieure de 95 %. L’API Personalizer n’applique les nouveaux paramètres d’apprentissage que dans les cas suivants :
  * Ils ont produit des résultats moyens plus élevés au cours de la période d’évaluation.
  * Ils ont une limite inférieure de 95 % de l’intervalle de confiance, qui est *supérieur* à la limite inférieure de 95% de l’intervalle de confiance des paramètres d’apprentissage en ligne.
Ces critères pour maximiser l’amélioration des résultats, tout en tentant d’éliminer la probabilité de dégradation des résultats futurs, sont gérés par l’API Personalizer et découlent de recherches dans les domaines des [algorithmes seldoniens](https://aisafety.cs.umass.edu/overview.html) et de la sécurité de l’intelligence artificielle.

#### <a name="limitations-of-auto-optimize"></a>Limitations de l’optimisation automatique

La fonctionnalité d’optimisation automatique de l’API Personalizer s’appuie sur l’évaluation d’une période passée pour estimer des performances à venir. Il se peut qu’en raison de facteurs externes, de votre application ou de vos utilisateurs, ces estimations et prédictions relatives aux modèles de l’API Personalizer effectuées au cours de la période passée ne soient pas représentatives du futur.

La préversion de l’optimisation automatique n’est pas disponible pour les boucles de l’API Personalizer qui ont activé la fonctionnalité en préversion de l’API Personnalisation sur plusieurs emplacements. 

## <a name="next-steps"></a>Étapes suivantes

* [Évaluations hors connexion](concepts-offline-evaluation.md)
* [Stratégie et paramètres d’apprentissage](concept-active-learning.md)
* [Comment analyser l’API Personalizer avec une évaluation hors connexion](how-to-offline-evaluation.md) 
* [Recherche sur la sécurité de l’intelligence artificielle](https://aisafety.cs.umass.edu/overview.html) 
