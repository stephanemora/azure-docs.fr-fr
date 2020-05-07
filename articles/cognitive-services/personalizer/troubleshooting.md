---
title: Résolution des problèmes - Personalizer
description: Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80336032"
---
# <a name="personalizer-troubleshooting"></a>Résolution des problèmes de Personalizer

Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.

## <a name="transaction-errors"></a>Erreurs des transactions

<details>
<summary><b>J’obtiens une réponse HTTP 429 (Trop de demandes) du service. Que puis-je faire ?</b></summary>

**Réponse** : Si vous avez choisi un niveau tarifaire gratuit lorsque vous avez créé l’instance Personalizer, vous avez une limite de quota sur le nombre de demandes de classement autorisées. Consultez votre taux d’appels d’API pour l’API Rank (dans le volet Métriques du portail Azure pour votre ressource Personalizer) et ajustez le niveau tarifaire (dans le volet Niveau tarifaire) si votre volume d’appels est supposé augmenter au-delà du seuil du niveau tarifaire choisi.

</details>

<details>
<summary><b>J’obtiens une erreur 5xx sur les API Rank ou Reward. Que dois-je faire ?</b></summary>

**Réponse** : Ces problèmes doivent être transparents. S’ils persistent, contactez le support en sélectionnant **Nouvelle demande de support** dans la section **Support + dépannage** du portail Azure pour votre ressource Personalizer.

</details>

## <a name="learning-loop"></a>Boucle d’apprentissage

<details>
<summary>
<b>La boucle d’apprentissage n’atteint pas une correspondance de 100 % avec le système sans Personalizer. Comment résoudre ce problème ?</b></summary>

**Réponse** : Les raisons pour lesquelles vous n’atteignez pas votre objectif avec la boucle d’apprentissage sont les suivantes :
* Nombre insuffisant de fonctionnalités envoyées avec l’appel d’API de classement
* Bogues dans les fonctionnalités envoyées, par exemple l’envoi de données de fonctionnalités non agrégées telles que les horodatages à l’API de classement
* Bogues au niveau du traitement de boucle, comme le fait de ne pas envoyer de données de récompense à l’API de récompense pour les événements

Pour résoudre le problème, vous devez modifier le traitement en changeant les fonctionnalités envoyées à la boucle, ou vérifier que la récompense est une évaluation correcte de la qualité de la réponse du classement.

</details>

<details>
<summary>
<b>La boucle d’apprentissage ne semble pas apprendre. Comment résoudre ce problème ?</b></summary>

**Réponse** : La boucle d’apprentissage a besoin de quelques milliers d’appels Reward avant que les appels Rank n’établissent des priorités efficaces.

En cas de doute sur le comportement actuel de votre boucle d’apprentissage, exécutez une [évaluation hors ligne](concepts-offline-evaluation.md) et appliquez la stratégie d’apprentissage corrigée.

</details>

<details>
<summary><b>Je continue à obtenir des résultats de classement avec les mêmes probabilités pour tous les éléments. Comment savoir si Personalizer apprend ?</b></summary>

**Réponse** : Personalizer retourne les mêmes probabilités dans un résultat d’API Rank lorsqu’il vient de démarrer et qu’il a un modèle _vide_, ou lorsque vous réinitialisez la boucle de Personalizer et que votre modèle est toujours dans la période de **Fréquence de mise à jour du modèle**.

Lorsque la nouvelle période de mise à jour commence, le modèle mis à jour est utilisé et vous voyez les probabilités changer.

</details>

<details>
<summary><b>La boucle d’apprentissage apprenait, mais semble ne plus apprendre, et la qualité des résultats de classement n’est pas si bonne. Que dois-je faire ?</b></summary>

**Réponse** :
* Veillez à effectuer et appliquer une évaluation dans le portail Azure pour cette ressource Personalizer (boucle d’apprentissage).
* Assurez-vous que toutes les récompenses sont envoyées via l’API Reward et qu’elles sont traitées.

</details>


<details>
<summary><b>Comment savoir si la boucle d’apprentissage est mise à jour régulièrement et utilisée pour établir le score de mes données ?</b></summary>

**Réponse** : Vous pouvez trouver l’heure de la dernière mise à jour du modèle dans la page **Paramètres de modèle et d’apprentissage** du portail Azure. Si vous voyez un ancien horodatage, c’est probablement parce que vous n’envoyez pas les appels Rank et Reward. Si le service n’a pas de données entrantes, il ne met pas à jour l’apprentissage. Si vous voyez que la boucle d’apprentissage n’est pas mise à jour assez souvent, vous pouvez modifier la **Fréquence de mise à jour du modèle** de la boucle.

</details>

## <a name="offline-evaluations"></a>Évaluations hors connexion

<details>
<summary><b>L’importance d’une caractéristique d’une évaluation hors connexion retourne une longue liste avec des centaines ou des milliers d’éléments. Que s’est-il passé ?</b></summary>

**Réponse** : C’est généralement dû aux horodateurs, aux ID d’utilisateur ou à d’autres caractéristiques très spécifiques qui y ont été envoyées.

</details>

<details>
<summary><b>J’ai créé une évaluation hors connexion et celle-ci a réussi presque instantanément. Pourquoi ? Je ne vois aucun résultat.</b></summary>

**Réponse** : L’évaluation hors connexion utilise les données de modèles entraînés des événements de cette période. Si vous n’avez pas envoyé de données pendant la période entre l’heure de début et l’heure de fin de l’évaluation, celle-ci ne produit aucun résultat. Procédez à une nouvelle évaluation hors connexion en sélectionnant une plage horaire avec les événements dont vous savez qu’ils ont été envoyés à Personalizer.

</details>


## <a name="learning-policy"></a>Stratégie d’apprentissage

<details>
<summary><b>Comment importer une stratégie d’apprentissage ?</b></summary>

**Réponse** : Découvrez les [concepts de la stratégie d’apprentissage](concept-active-learning.md#understand-learning-policy-settings) et [comment appliquer](how-to-manage-model.md) une nouvelle stratégie d’apprentissage. Si vous ne voulez pas sélectionner une stratégie d’apprentissage, vous pouvez utiliser l’[évaluation hors connexion](how-to-offline-evaluation.md) pour suggérer une stratégie d’apprentissage en fonction de vos événements actuels.

</details>

## <a name="security"></a>Sécurité

<details>
<summary><b>La clé API de ma boucle a été compromise. Que puis-je faire ?</b></summary>

**Réponse** : Vous pouvez régénérer une clé après avoir demandé à vos clients de changer et d’utiliser l’autre clé. Le fait de disposer de deux clés vous permet de propager la clé de manière différée sans avoir besoin de temps d’arrêt. Nous vous recommandons de le faire au cours d’un cycle normal comme mesure de sécurité.

</details>

## <a name="next-steps"></a>Étapes suivantes

[Configurer la fréquence de mise à jour du modèle](how-to-settings.md#model-update-frequency)