---
title: Bonnes pratiques - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370872"
---
# <a name="best-practices"></a>Bonnes pratiques
Le [cycle de vie du développement d’une base de connaissances](../Concepts/development-lifecycle-knowledge-base.md) vous guide de bout en bout sur la façon de gérer votre base de connaissances. Suivez ces bonnes pratiques pour améliorer votre base de connaissances et fournir des résultats plus pertinents aux utilisateurs finaux de votre application/bot conversationnel.

## <a name="extraction"></a>Extraction
QnA Maker améliore en permanence les algorithmes qui extraient des entités QnA à partir du contenu, ainsi que la prise en charge des formats de fichier et de page HTML. Suivez les [conseils](../Concepts/data-sources-supported.md) sur l’extraction correspondant au type de votre document source. 

En général, les pages FAQ doivent être autonomes et ne pas être combinées avec d’autres informations. Les manuels de produits doivent avoir des titres clairs et, si possible, une page d’index. 

## <a name="rankingmatching"></a>Classement et correspondance
Essayez d’optimiser l’utilisation des fonctionnalités de classement que QnA Maker prend en charge. Ces fonctionnalités augmentent la probabilité de retourner une réponse pertinente à une requête utilisateur.

### <a name="add-alternate-questions"></a>Ajouter des questions alternatives
Les [questions alternatives](../How-To/edit-knowledge-base.md) augmentent la probabilité de trouver une correspondance avec une requête utilisateur. Les questions alternatives sont utiles quand il existe plusieurs manières de poser une question. C’est notamment le cas pour les variantes syntaxiques (par exemple, *« Est-ce possible de se garer ? »* et *« Avez-vous un parking ?»*) ou les variantes stylistiques et termes argotiques (par exemple, *« Salut »*, *« Yo »*, *« Coucou ! »*).

### <a name="use-metadata-filters"></a>Utiliser des filtres de métadonnées
Les [métadonnées](../How-To/edit-knowledge-base.md) vous permettent aussi de réduire le nombre des résultats d’une requête utilisateur en les filtrant. La réponse de la base de connaissances peut varier en fonction du mot clé de métadonnées, même si la requête est la même. Par exemple, dans le cas d’une chaîne de restaurants, la question *« Où se trouve le parking ? »* peut donner une réponse différente en fonction du lieu du restaurant (ici, les métadonnées sont *Lieu : Seattle* et *Lieu : Redmond*.)

### <a name="use-synonyms"></a>Utiliser des synonymes
Les synonymes en langue anglaise sont pris en charge. Utilisez des [variantes de mots](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) afin d’ajouter des synonymes à des mots clés qui prennent différentes formes (par exemple, *payer* -> *régler* ou *banque en ligne* -> *banque sur Internet*). Les synonymes doivent être ajoutés au niveau du service QnA Maker et être partagés par toutes les bases de connaissances du service.

### <a name="use-distinct-words-to-differentiate-questions"></a>Utiliser des mots distincts pour différencier les questions
Les algorithmes de correspondance et de classement de QnA Maker recherchent une correspondance entre une requête utilisateur et l’une des questions contenues dans la base de connaissances. Ils retournent de meilleurs résultats si chaque question répond à des besoins différents. La répétition du même mot défini entre les questions réduit la probabilité de sélection de la réponse appropriée pour une requête utilisateur qui contient ces mots.

## <a name="collaborate"></a>Travailler en collaboration
QnA Maker permet aux utilisateurs de [travailler en collaboration](../How-to/collaborate-knowledge-base.md) sur une base de connaissances. Les utilisateurs doivent avoir accès au groupe de ressources Azure QnA Maker pour accéder à la base de connaissances. Certaines organisations souhaitent externaliser la mise à jour et la gestion des bases de connaissances tout en pouvant quand même protéger l’accès à leurs ressources Azure. Ce modèle éditeur-approbateur peut être mis en place en configurant deux [services QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identiques dans des abonnements différents et en utilisant l’un d’entre eux pour le cycle de modification-test. À la fin des tests, le contenu de la base de connaissances peut être transféré, par un processus [importation-exportation](../Tutorials/migrate-knowledge-base.md), au service QnA Maker de l’approbateur, lequel publiera la base de connaissances et mettra à jour le point de terminaison.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Modifier une base de connaissances](../How-to/edit-knowledge-base.md)

