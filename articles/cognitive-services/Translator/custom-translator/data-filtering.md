---
title: Filtrage des données - Custom Translator
titleSuffix: Azure Cognitive Services
description: Lorsque vous soumettez des documents pour l'apprentissage d'un système personnalisé, différentes étapes de traitement et de filtrage s'appliquent à ceux-ci afin de préparer l'apprentissage.
author: v-pawal
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-jansko
ms.topic: conceptual
ms.openlocfilehash: 0871cb7e4dcbe8cf71f35f174137396bde607c54
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916105"
---
# <a name="data-filtering"></a>Filtrage de données

Lorsque vous soumettez des documents pour l'apprentissage d'un système personnalisé, différentes étapes de traitement et de filtrage s'appliquent à ceux-ci afin de préparer l'apprentissage. Ces étapes sont décrites ici. La connaissance du filtrage peut vous aider à comprendre le nombre de phrases affichées dans Custom Translator ainsi que les étapes à suivre pour préparer les documents en vue d'un apprentissage avec Custom Translator.

## <a name="sentence-alignment"></a>Alignement de phrase
Si votre document n'est pas au format XLIFF, TMX ou ALIGN, Custom Translator aligne les phrases de vos documents source et cible, phrase par phrase. Translator ne procède pas à l'alignement des documents. Il suit les noms que vous avez donnés aux documents afin de trouver le document correspondant dans l'autre langue. Dans le document, Custom Translator essaie de trouver la phrase correspondante dans l'autre langue. Il a recours au balisage des documents (balises HTML incorporées, par exemple) pour faciliter l'alignement.  

Si vous consultez une grande différence entre le nombre de phrases dans la source d’et ciblez des documents côté, votre document ne peut-être pas encore été parallèle en premier lieu, ou pour d’autres raisons n’a pas pu être aligné. Les paires de documents présentant une importante différence de phrases (> 10 %) de chaque côté nécessitent un second examen pour s'assurer qu'elles sont bien parallèles. Custom Translator affiche un avertissement en regard du document en cas de différence suspecte du nombre de phrases.  


## <a name="deduplication"></a>Déduplication
Custom Translator supprime des données d'apprentissage les phrases présentes dans les documents de test et d'optimisation. La suppression s'effectue dynamiquement pendant l'apprentissage, et non lors de l'étape de traitement des données. Custom Translator affiche le nombre de phrases dans la vue d'ensemble du projet avant la suppression.  

## <a name="length-filter"></a>Filtre de longueur
* Suppression des phrases d'un seul mot de chaque côté.
* Suppression des phrases de plus de 100 mots de chaque côté.  Le chinois, le japonais et le coréen sont exemptés.
* Suppression des phrases de moins de 3 caractères. Le chinois, le japonais et le coréen sont exemptés.
* Suppression des phrases de plus de 2 000 caractères pour le chinois, le japonais, le coréen.
* Suppression des phrases contenant moins de 1 % de caractères alpha.
* Suppression des entrées de dictionnaire contenant plus de 50 mots.

## <a name="white-space"></a>Espace blanc
* Remplacement de toutes les séquences d'espaces blancs, y compris des tabulations et des séquences Retour chariot/Saut de ligne, par un seul espace.
* Suppression des espaces de début ou de fin dans la phrase.

## <a name="sentence-end-punctuation"></a>Ponctuation de fin de phrase
Remplacement les caractères de ponctuation de fin de phrase multiples par une seule instance.  

## <a name="japanese-character-normalization"></a>Normalisation des caractères japonais
Convertir en caractères demi-chasse pleine chasse lettres et chiffres.

## <a name="unescaped-xml-tags"></a>Balises XML sans séquence d'échappement
Le filtrage transforme les balises sans séquence d'échappement en balises avec séquence d'échappement :
* `&lt;` devient `&amp;lt;`
* `&gt;` devient `&amp;gt;`
* `&amp;` devient `&amp;amp;`

## <a name="invalid-characters"></a>Caractères non valides
Custom Translator supprime les phrases contenant le caractère Unicode U+FFFD. Le caractère U+FFFD signale l'échec d'une conversion d'encodage.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer l'apprentissage d'un modèle](how-to-train-model.md) dans Custom Translator.
