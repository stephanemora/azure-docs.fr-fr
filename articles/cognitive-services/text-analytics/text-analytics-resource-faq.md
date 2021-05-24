---
title: Questions fréquentes sur l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Trouvez les réponses aux questions courantes sur les concepts, le code et les scénarios relatifs à l’API Analyse de texte pour Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 324ff6f29aef76ef5baded89db92bb4e4b5ec362
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108755546"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Questions fréquentes (FAQ) sur l’API Analyse de texte

 Trouvez les réponses aux questions courantes sur les concepts, le code et les scénarios relatifs à l’API Analyse de texte pour Azure Cognitive Services.

## <a name="what-is-the-maximum-size-and-number-of-requests-i-can-make-to-the-api"></a>Quelle est la taille maximale et le nombre de requêtes que je peux envoyer à l’API ?

Consultez l’article [Limites de données](concepts/data-limits.md) pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et seconde.

## <a name="can-text-analytics-identify-sarcasm"></a>L’analyse de texte permet-elle de repérer le sarcasme ?

L’analyse concerne les sentiments négatifs et positifs plutôt que la détection de l’humeur.

Il y a toujours une certaine imprécision dans l’analyse des sentiments, mais le modèle est particulièrement efficace lorsqu’il n’existe aucun sous-entendu dans le contenu. L’ironie, le sarcasme, l’humeur et tout contenu nuancé reposent sur un contexte culturel et des normes. Ce type de contenu est parmi les plus difficiles à analyser. En règle générale, le plus grand écart entre un score donné produit par l’analyseur et une évaluation subjective par un être humain concerne le contenu nuancé.

## <a name="can-i-add-my-own-training-data-or-models"></a>Puis-je ajouter mes propres données de formation ou mes propres modèles ?

Non, les modèles sont préintégrés. Les seules opérations disponibles sur les données téléchargées sont le calcul du score, l’extraction d’expressions clés et la détection de la langue. Nous n’hébergeons pas de modèles personnalisés. Si vous souhaitez créer et héberger des modèles de Machine Learning personnalisés, tournez-vous vers [les modèles de Machine Learning de Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Puis-je demander des langues supplémentaires ?

L’analyse des sentiments et l’extraction des expressions clés sont disponibles pour un [certain nombre de langues](./language-support.md). Le traitement du langage naturel est complexe et nécessite de nombreux tests avant de pouvoir publier de nouvelles fonctionnalités. Pour cette raison, nous évitons d’annoncer une prise en charge à l’avance, afin que personne n’attende une fonctionnalité dont le développement nécessite plus de temps que prévu. 

Pour nous aider à hiérarchiser les langues à ajouter, votez pour des langues spécifiques dans l’[outil de commentaires](https://feedback.azure.com/forums/932041-azure-cognitive-services?category_id=395749). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Pourquoi l’extraction d’expressions clés renvoie-t-elle certains mots, mais pas d’autres ?

L’extraction des expressions clés élimine les mots non essentiels et les adjectifs isolés. Les combinaisons adjectif-nom, telles que « vues spectaculaires » ou « temps brumeux », sont renvoyées ensemble.

En règle générale, la sortie se compose des noms et des objets de la phrase. La sortie est classée par ordre d’importance, la première expression étant la plus importante. L’importance est mesurée par le nombre de fois où un concept spécifique est mentionné, ou par la relation de cet élément avec d’autres éléments dans le texte.

## <a name="why-does-output-vary-given-identical-inputs"></a>Pourquoi la sortie varie-t-elle même si les entrées sont identiques ?

Les améliorations apportées aux modèles et aux algorithmes sont annoncées lorsque la modification est importante ou si elle est intégrée en mode silencieux dans le service lorsque la mise à jour est mineure. Au fil du temps, vous pourrez constater que les mêmes entrées de texte engendrent un autre score de sentiment ou une sortie d’expressions clés différente. Il s’agit d’une conséquence normale et intentionnelle de l’utilisation des ressources de Machine Learning dans le cloud.

## <a name="service-availability-and-redundancy"></a>Disponibilité et redondance du service

### <a name="is-text-analytics-service-zone-resilient"></a>Le service Analyse de texte est-il résilient aux zones ?

Oui. Le service Analyse de texte est résilient aux zones par défaut.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Comment configurer le service Analyse de texte pour le rendre résilient aux zones ?

Aucune configuration client n’est nécessaire pour activer la résilience des zones. La résilience aux zones pour les ressources Analyse de texte est disponible par défaut et gérée par le service lui-même.

## <a name="next-steps"></a>Étapes suivantes

Votre question concerne-t-elle une fonctionnalité manquante ? Envisagez de la demander ou de la voter à l’aide de l’[outil de commentaires](https://feedback.azure.com/forums/932041-azure-cognitive-services?category_id=395749).

## <a name="see-also"></a>Voir aussi

 * [StackOverflow : API Analyse de texte](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow : Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
