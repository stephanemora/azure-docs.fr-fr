---
title: Instructions pour le déploiement responsable de la technologie des voix de synthèse
titleSuffix: Azure Cognitive Services
description: Instructions de conception générale de Microsoft pour l’utilisation de la technologie des voix de synthèse. Elles ont été développées dans des études menées par Microsoft auprès de voix professionnelles, de consommateurs, ainsi que de personnes ayant des troubles de la parole pour guider le développement responsable des voix de synthèse.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 715c09ef65358b21e78cfde204b4819db0c7875d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428417"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Instructions pour le déploiement responsable de la technologie des voix de synthèse

## <a name="general-considerations-to-keep-in-mind-when-implementing-ai-systems"></a>Considérations générales à prendre en compte lors de l’implémentation de systèmes d’intelligence artificielle (IA) 

Cet article traite spécifiquement de la reconnaissance vocale synthétique et de la voix neuronale personnalisée, ainsi que des considérations essentielles pour l’utilisation de cette technologie de manière responsable. En général, il existe cependant plusieurs éléments à prendre en compte attentivement lorsque vous décidez de l’utilisation et de l’implémentation de produits et fonctionnalités d’intelligence artificielle : 

* Ce produit ou cette fonctionnalité s’exécutent-ils correctement dans mon scénario ? Avant de déployer une intelligence artificielle dans votre scénario, testez son fonctionnement à l’aide de données réelles, et assurez-vous qu’elle peut fournir la précision dont vous avez besoin. 
* Sommes-nous équipés pour identifier les erreurs et y répondre ? Les produits et fonctionnalités d’IA ne seront pas toujours d’une précision absolue. Par conséquent, réfléchissez à la façon dont vous allez identifier et résoudre les erreurs qui peuvent se produire. 

## <a name="general-guidelines-for-using-synthetic-voice-technology"></a>Recommandations générales pour l’utilisation de la technologie des voix de synthèse 
Voici les instructions de conception générale de Microsoft pour l’utilisation de la technologie des voix de synthèse. Elles ont été développées dans des études menées par Microsoft auprès de voix professionnelles, de consommateurs, ainsi que de personnes ayant des troubles de la parole pour guider le développement responsable des voix de synthèse.

Pour le déploiement de la technologie des voix de synthèse, les instructions suivantes s’appliquent dans la plupart des scénarios.

### <a name="disclose-when-the-voice-is-synthetic"></a>Révéler qu’il s’agit d’une voix de synthèse
Le fait de révéler qu’une voix est générée par ordinateur permet de réduire les déceptions éventuelles, mais aussi d’augmenter la confiance en l’organisation qui fournit la voix. En savoir plus sur [comment le révéler](concepts-disclosure-guidelines.md).

Microsoft oblige ses clients à divulguer la nature synthétique de la voix neuronale personnalisée à ses utilisateurs. 
* Veillez à fournir une divulgation appropriée aux personnes concernées, en particulier lors de l’utilisation de la voix d’une personne connue. Que ce soit de manière consciente ou inconsciente, nous fondons notre opinion sur des informations en fonction de la personne qui les transmet.  Par exemple, la divulgation peut être effectuée verbalement au début d’un programme. Pour plus d’informations, consultez les [modèles de divulgation](concepts-disclosure-patterns.md).   
* Choisissez une divulgation qui cible les parents ou d’autres parties dans les cas d’utilisation destinés aux mineurs et aux enfants. Si votre cas d’utilisation implique des mineurs ou des enfants, vous devrez vous assurer que les parents ou les tuteurs légaux sont en mesure de comprendre la divulgation sur l’utilisation des médias synthétiques et de prendre la bonne décision pour les mineurs ou les enfants quant à l’utilisation de l’expérience. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Sélectionner les types de voix appropriés pour votre scénario
Examinez attentivement le contexte d’utilisation et les risques potentiels associés à l’utilisation de voix de synthèse. Par exemple, des voix de synthèse très fidèles ne sont pas forcément appropriées pour les scénarios à haut risque, tels que les messages personnels, les transactions financières ou les situations complexes pour lesquels une certaine empathie ou humanité est nécessaire. Les utilisateurs peuvent également avoir des attentes différentes pour les types de voix. Par exemple, lorsqu’ils écoutent les dernières actualités sensibles lues par une voix de synthèse, certains utilisateurs préféreront un ton plus humain et plus empathique, tandis que d’autres aimeraient une voix plus monotone et neutre. Pensez à tester votre application pour mieux comprendre les préférences des utilisateurs.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Faire preuve de transparence au sujet des capacités et des limitations
Les utilisateurs auront vraisemblablement des attentes plus élevées s’ils interagissent avec des agents vocaux de synthèse. Par conséquent, lorsque les capacités du système sont en deçà de ces attentes, une baisse de confiance peut être observée et conduire à des expériences désagréables voire négatives.

### <a name="provide-optional-human-support"></a>Proposer une assistance humaine facultative
Dans certains scénarios transactionnels et ambigus (par exemple, un centre d’appel), les utilisateurs ne font pas toujours confiance à un agent virtuel pour répondre correctement à leurs demandes. Une assistance humaine peut être nécessaire dans ces situations, quels que soient le degré de réalisme de la voix ou les capacités du système.

## <a name="considerations-for-voice-talent"></a>Considérations relatives aux voix professionnelles
Lorsque vous travaillez avec des voix professionnelles (p. ex. des acteurs) pour créer des voix de synthèse, l’instruction suivante s’applique.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenir le consentement explicite des voix professionnelles
Les talents vocaux doivent avoir un contrôle sur leur modèle vocal (comment et où il sera utilisé) et être rémunérés chaque fois que ce modèle est utilisé. Microsoft oblige ses clients vocaux personnalisés à obtenir une autorisation écrite explicite de la part de leur talent vocal afin de créer une voix synthétique et l’accord de ce dernier quant à la durée, à l’utilisation et à toutes les limitations de contenu.  Si vous créez une voix synthétique d’une personne connue, vous devez fournir un moyen permettant à cette personne de modifier ou d’approuver le contenu.

Certains talents vocaux n’ont pas conscience des utilisations potentiellement malveillantes de la technologie ; les propriétaires du système doivent donc les informer à ce sujet. Microsoft oblige ses clients à partager la [Divulgation d’un talent vocal](/legal/cognitive-services/speech-service/disclosure-voice-talent) avec leur talent vocal directement ou par l’intermédiaire d’un représentant autorisé du talent vocal, document qui décrit le développement et l’utilisation des voix synthétiques conjointement avec les services de synthèse vocale.

## <a name="considerations-for-those-with-speech-disorders"></a>Considérations relatives aux personnes ayant des troubles de la parole
Lorsque vous travaillez avec des personnes ayant des troubles de la parole, pour créer ou déployer une technologie de voix de synthèse, les instructions suivantes s’appliquent.

### <a name="provide-guidelines-to-establish-contracts"></a>Fournir des instructions pour établir des contrats
Fournir des instructions pour établir des contrats avec les personnes qui utilisent les voix de synthèse pour s’exprimer. Le contrat doit indiquer les parties propriétaires de la voix, la durée d’utilisation, les critères de transfert de propriété, les procédures pour supprimer la police vocale et comment empêcher les accès non autorisés. En outre, permettez le transfert du contrat de propriété de la police vocale après le décès aux membres de la famille si la personne concernée en a donné l’autorisation.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Tenir compte des incohérences dans les modèles vocaux
Pour les personnes ayant des troubles de la parole et qui enregistrent leurs propres polices vocales, les incohérences dans leur modèle de discours (déformation ou incapacité à prononcer certains mots) peuvent compliquer le processus d’enregistrement. Dans ce cas, la technologie de voix de synthèse et des sessions d’enregistrement doivent les aider (autrement dit, proposer des pauses et d’autres sessions d’enregistrement).

### <a name="allow-modification-over-time"></a>Pouvoir appliquer des modifications ultérieurement
Les personnes ayant des troubles de la parole souhaitent modifier leur voix de synthèse pour qu’elle tienne compte de leur âge (par exemple, un enfant qui devient adolescent). Les utilisateurs peuvent également avoir des préférences stylistiques évoluant au fil du temps et peuvent souhaiter modifier la tonalité, l’accent ou d’autres caractéristiques vocales.


## <a name="reference-docs"></a>Documents de référence

* [Divulgation d’un talent vocal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Vue d’ensemble du contingentement](concepts-gating-overview.md)
* [Comment divulguer](concepts-disclosure-guidelines.md)
* [Modèles de conception de révélation](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Étapes suivantes

* [Divulgation d’un talent vocal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Comment divulguer](concepts-disclosure-guidelines.md)
* [Modèles de conception de révélation](concepts-disclosure-patterns.md)
