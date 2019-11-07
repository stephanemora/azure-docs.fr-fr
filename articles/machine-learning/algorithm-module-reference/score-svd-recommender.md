---
title: 'Noter le générateur de recommandations SVD : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Noter le générateur de recommandations SVD dans Azure Machine Learning service pour noter les prédictions de recommandation pour un jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511846"
---
# <a name="score-svd-recommender"></a>Noter le générateur de recommandations SVD

Cet article décrit comment utiliser le module **Noter le générateur de recommandations SVD** dans le concepteur Azure Machine Learning (préversion). Utilisez ce module pour créer des prédictions à l’aide d’un modèle de recommandation formé en fonction de l’algorithme SVD (décomposition à valeur unique).

Le générateur de recommandations SVD peut générer deux différents types de prédictions :

- [Prédire des notations pour un utilisateur et un élément donné](#predict-ratings)

- [Recommander des éléments à un utilisateur donné](#recommend)

Lorsque vous créez le deuxième type de prédictions, vous pouvez travailler en *mode de production* ou en *mode d’évaluation*.

- Le **mode de production** prend en compte tous les utilisateurs ou éléments, et est généralement utilisé dans un service web.

    Vous pouvez créer des scores pour les nouveaux utilisateurs, pas seulement pour les utilisateurs vus au cours de l’apprentissage. Pour plus d’informations, consultez [cette section](#technical-notes). 

- Le **mode d’évaluation** fonctionne sur un ensemble réduit d’utilisateurs ou d’éléments qui peut être évalué, et est généralement utilisé pendant l’exécution du pipeline.

Pour plus d’informations sur l’algorithme du générateur de recommandations SVD, consultez le document de recherche : [Techniques de factorisation de matrice pour les systèmes de recommandation](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Comment configurer le module Noter le générateur de recommandations SVD

Ce module prend en charge différents types de recommandations, chacune avec des exigences différentes. Cliquez sur le lien correspondant au type de données que vous avez et au type de recommandation que vous souhaitez créer.

+ [Prédire des notations](#predict-ratings)
+ [Recommander des éléments](#recommend)

###  <a name="predict-ratings"></a>Prédire des notations

Lorsque vous prédisez des notations, le modèle calcule la manière dont un utilisateur donné réagira à un élément particulier, en fonction des données d’apprentissage. Par conséquent, les données d’entrée pour le calcul de score doivent fournir à la fois l’utilisateur et l’élément à évaluer.

1. Ajoutez un modèle de recommandation formé à votre pipeline et connectez-le au **Générateur de recommandations SVD entraîné**.  Vous devez créer le modèle à l’aide du module [Entraîner le générateur de recommandations SVD](train-SVD-recommender.md).

2. **Type de prédiction de recommandation** : Sélectionnez **Prédiction de notations**. Aucun paramètre supplémentaire n’est nécessaire.

3. Ajoutez les données pour lesquelles vous souhaitez effectuer des prédictions et connectez-les au **Jeu de données à noter**.

    Pour prédire les notations, le jeu de données d’entrée doit contenir des paires utilisateur-élément.

    Le jeu de données peut contenir une troisième colonne de notation facultative pour la paire utilisateur-élément dans la première et la deuxième colonne, mais la troisième colonne sera ignorée pendant la prédiction.

4. Exécuter le pipeline.

### <a name="results-for-rating-predictions"></a>Résultats des prédictions de notation 

Le jeu de données de sortie contient trois colonnes, contenant l’utilisateur, l’élément, et la notation prédite pour chaque utilisateur et élément d’entrée.

###  <a name="recommend"></a>Recommander 

Pour recommander des éléments pour les utilisateurs, vous fournissez une liste d’utilisateurs et d’éléments comme entrée. À partir de ces données, le modèle utilise ses connaissances sur les éléments et les utilisateurs existants afin de générer une liste d’éléments avec un attrait probable pour chaque utilisateur. Vous pouvez personnaliser le nombre de recommandations renvoyées et définir un seuil pour le nombre de recommandations précédentes requises pour générer une recommandation.

1. Ajoutez un modèle de recommandation formé à votre pipeline et connectez-le au **Générateur de recommandations SVD entraîné**.  Vous devez créer le modèle à l’aide du module [Entraîner le générateur de recommandations SVD](train-svd-recommender.md).

2. Pour recommander des éléments pour une liste donnée d’utilisateurs, définissez **Type de prédiction de recommandation** sur **Recommandation d’éléments**.

3. **Sélection d’éléments recommandés** : Indiquez si vous utilisez le module de notation en production ou pour l’évaluation de modèle, en choisissant l’une des valeurs suivantes :

    - **À partir de tous les éléments** : Sélectionnez cette option si vous configurez un pipeline pour une utilisation dans un service web ou en production.  Cette option active le **mode de production**, le module émet alors des recommandations à partir de tous les éléments vus pendant l’entraînement.

    - **À partir d’éléments notés (pour l’évaluation du modèle)** : Sélectionnez cette option si vous développez ou testez un modèle. Cette option active le **mode d’évaluation**, le module génère alors des recommandations uniquement à partir des éléments du jeu de données d’entrée qui ont été évalués.
    
    - **À partir d’éléments non notés (pour suggérer de nouveaux éléments aux utilisateurs)** : Sélectionnez cette option et le module générera des recommandations uniquement à partir des éléments du jeu de données d’entrée qui ont été évalués. 

4. Ajoutez le jeu de données pour lequel vous souhaitez effectuer des prédictions et connectez-le au **Jeu de données à noter**.

    - Pour **À partir de tous les éléments**, le jeu de données d’entrée doit se composer d’une colonne, contenant les identificateurs des utilisateurs pour lesquels formuler des recommandations.

        Le jeu de données peut inclure deux colonnes d’identificateurs d’éléments et de notations supplémentaires, mais ces deux colonnes sont ignorées. 

    - Pour **À partir d’éléments notés (pour l’évaluation du modèle)** , le jeu de données d’entrée doit être composé de **paires utilisateur-élément**. La première colonne doit contenir l’identificateur d’**utilisateur**. La deuxième colonne doit contenir les identificateurs d’**élément** correspondants.

        Le jeu de données peut inclure une troisième colonne de notations utilisateur-élément, mais cette colonne est ignorée.

    - Pour **À partir d’éléments non notés (pour suggérer de nouveaux éléments aux utilisateurs)** , le jeu de données d’entrée doit être composé de **paires utilisateur-élément**. La première colonne doit contenir l’identificateur d’**utilisateur**. La deuxième colonne doit contenir les identificateurs d’**élément** correspondants.

        Le jeu de données peut inclure une troisième colonne de notations utilisateur-élément, mais cette colonne est ignorée.

5. **Nombre maximal d’éléments à recommander à un utilisateur** : Saisissez le nombre d’éléments à retourner pour chaque utilisateur. Par défaut, cinq éléments sont recommandés.

6. **Taille minimale du pool de recommandations par utilisateur** : Saisissez une valeur qui indique le nombre de recommandations antérieures requises.  Par défaut, ce paramètre est défini sur 2, ce qui signifie que l’élément doit avoir été recommandé par au moins deux autres utilisateurs.

    Cette option ne doit être utilisée que si vous effectuez une notation en mode évaluation. L’option n’est pas disponible si vous sélectionnez **À partir de tous les éléments** ou **À partir d’éléments non notés (pour suggérer de nouveaux éléments aux utilisateurs)** .

7.  Pour **À partir d’éléments non notés (pour suggérer de nouveaux éléments aux utilisateurs)** , utilisez le troisième port d’entrée nommé **Données d’entraînement** pour supprimer les éléments qui ont déjà été notés dans les résultats de prédiction.

    Pour appliquer ce filtre, connectez le jeu de données d’entraînement d’origine au port d’entrée.

8. Exécuter le pipeline.

### <a name="results-of-item-recommendation"></a>Résultats de la recommandation d’élément

Le jeu de données noté renvoyé par **Noter le générateur de recommandations SVD** répertorie les éléments recommandés pour chaque utilisateur.

- La première colonne contient les identificateurs d’utilisateur.
- Plusieurs colonnes supplémentaires sont générées, en fonction de la valeur que vous avez définie pour **Nombre maximal d’éléments à recommander à un utilisateur**. Chaque colonne contient un élément recommandé (par identificateur). Les recommandations sont classées par affinité utilisateur-élément, l’élément disposant de l’affinité la plus élevée est placé dans la colonne **Élément 1**.

> [!WARNING]
> Ce jeu de données noté ne peut pas être évalué à l’aide du module [Évaluer le générateur de recommandations](evaluate-recommender.md).


##  <a name="technical-notes"></a>Notes techniques

Cette section contient des réponses à certaines questions courantes sur l’utilisation du générateur de recommandations pour créer des prédictions.  

###  <a name="production-use-of-the-svd-recommender"></a>Utilisation du générateur de recommandations SVD en production

Si vous avez un pipeline avec le générateur de recommandations SVD et que vous déplacez le modèle en production, tenez compte de ces différences clés lors de l’utilisation du générateur de recommandations en mode évaluation et en mode production :

- L’évaluation, par définition, requiert des prédictions qui peuvent être vérifiées par rapport à la *vérité de terrain* dans un jeu de test. Par conséquent, lorsque vous évaluez le générateur de recommandations, il doit prédire uniquement les éléments qui ont été évalués dans le jeu de test. Cela restreint nécessairement les valeurs possibles qui sont prédites.

    Toutefois, lorsque vous procédez au fonctionnement du modèle, vous modifiez généralement le mode de prédiction pour effectuer des recommandations en fonction de tous les éléments possibles, afin d’obtenir les meilleures prédictions. Pour la plupart de ces prédictions, il n’y a pas de vérité de terrain correspondante, ainsi la précision de la recommandation ne peut pas être vérifiée de la même façon que lors de l’utilisation d’un pipeline.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 
