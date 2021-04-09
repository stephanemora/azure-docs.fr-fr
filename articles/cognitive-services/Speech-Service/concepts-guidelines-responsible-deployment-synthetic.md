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
ms.openlocfilehash: 3a0b645acd7c21ff0416c748cdd2caf7041be508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730811"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Instructions pour le déploiement responsable de la technologie des voix de synthèse

Dans cet article, vous allez découvrir les instructions de conception générale de Microsoft relatives à l’utilisation de la technologie des voix de synthèse. Ces instructions ont été développées dans des études menées par Microsoft auprès de voix professionnelles, de consommateurs et de personnes ayant des troubles de la parole pour guider le développement responsable des voix de synthèse.

Pour le déploiement de la technologie des voix de synthèse, les instructions suivantes s’appliquent dans la plupart des scénarios.

### <a name="disclose-when-the-voice-is-synthetic"></a>Révéler qu’il s’agit d’une voix de synthèse
Le fait de révéler qu’une voix est générée par ordinateur permet de réduire les déceptions éventuelles, mais aussi d’augmenter la confiance en l’organisation qui fournit la voix. En savoir plus sur [comment le révéler](concepts-disclosure-guidelines.md).

Microsoft oblige ses clients à divulguer la nature synthétique de la voix neuronale personnalisée à ses utilisateurs. 
* Veillez à fournir une divulgation appropriée aux personnes concernées, en particulier lors de l’utilisation de la voix d’une personne connue. Que ce soit de manière consciente ou inconsciente, nous fondons notre opinion sur des informations en fonction de la personne qui les transmet.  Par exemple, la divulgation peut être effectuée verbalement au début d’un programme. Pour plus d’informations, consultez les [modèles de divulgation](concepts-disclosure-patterns.md).   
* Choisissez une divulgation qui cible les parents ou d’autres parties dans les cas d’utilisation destinés aux mineurs et aux enfants. Si votre cas d’utilisation implique des mineurs ou des enfants, vous devrez vous assurer que les parents ou les tuteurs légaux sont en mesure de comprendre la divulgation sur l’utilisation des médias synthétiques et de prendre la bonne décision pour les mineurs ou les enfants quant à l’utilisation de l’expérience. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Sélectionner les types de voix appropriés pour votre scénario
Examinez attentivement le contexte d’utilisation et les risques potentiels associés à l’utilisation de voix de synthèse. Par exemple, des voix de synthèse très fidèles ne sont pas forcément appropriées pour les scénarios à haut risque, tels que les messages personnels, les transactions financières ou les situations complexes pour lesquels une certaine empathie ou humanité est nécessaire. 

Les utilisateurs peuvent également avoir des attentes différentes pour les types de voix. Par exemple, lorsqu’ils écoutent les dernières actualités sensibles lues par une voix de synthèse, certains utilisateurs préféreront un ton plus humain et plus empathique, tandis que d’autres aimeraient une voix neutre. Pensez à tester votre application pour mieux comprendre les préférences des utilisateurs.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Faire preuve de transparence au sujet des capacités et des limitations
Les utilisateurs auront vraisemblablement des attentes plus élevées s’ils interagissent avec des agents vocaux de synthèse. Lorsque les capacités du système sont en deçà de ces attentes, une baisse de confiance peut être observée et conduire à des expériences désagréables, voire négatives.

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
Fournir des instructions pour établir des contrats avec les personnes qui utilisent les voix de synthèse pour s’exprimer. Le contrat doit indiquer les parties propriétaires de la voix, la durée d’utilisation, les critères de transfert de propriété, les procédures pour supprimer la police vocale et comment empêcher les accès non autorisés. En outre, permettez le transfert de propriété de la police de la voix après le décès aux membres de la famille, si une autorisation a été accordée.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Tenir compte des incohérences dans les modèles vocaux
Pour les personnes ayant des troubles de la parole et qui enregistrent leurs propres polices vocales, les incohérences dans leur modèle de discours (déformation ou incapacité à prononcer certains mots) peuvent compliquer le processus d’enregistrement. Dans ce cas, la technologie de voix de synthèse et des sessions d’enregistrement doivent les aider (autrement dit, proposer des pauses et d’autres sessions d’enregistrement).

### <a name="allow-modification-over-time"></a>Pouvoir appliquer des modifications ultérieurement
Les personnes ayant des troubles de la parole souhaitent modifier leur voix de synthèse pour qu’elle tienne compte de leur âge (par exemple, un enfant qui devient adolescent). Les utilisateurs peuvent également avoir des préférences stylistiques évoluant au fil du temps et peuvent souhaiter modifier la tonalité, l’accent ou d’autres caractéristiques vocales.


## <a name="see-also"></a>Voir également

* [Divulgation d’un talent vocal](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Comment divulguer](concepts-disclosure-guidelines.md)
* [Modèles de conception de révélation](concepts-disclosure-patterns.md)