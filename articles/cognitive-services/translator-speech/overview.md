---
title: Documentation de l’API Traduction vocale | Microsoft Docs
titleSuffix: Cognitive Services
description: Utilisez l’API de traduction de conversation Microsoft Translator Speech pour ajouter la traduction voix-voix et voix-texte à vos applications.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368541"
---
# <a name="microsoft-translator-speech-api"></a>API de traduction de conversation Microsoft Translator Speech
L’API de traduction de conversation Microsoft Translator Speech permet d’ajouter des traductions vocales en temps réel et de bout en bout aux applications, outils ou toute autre solution requérant une traduction vocale en plusieurs langues, indépendamment du système d’exploitation cible ou des langues de développement. L’API peut être utilisée pour la traduction voix-voix et la traduction voix-texte.

L’API de traduction de texte Microsoft Translator est un service Azure et fait partie de la [collection d’API Microsoft Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) d’algorithmes d’apprentissage automatique et d’intelligence artificielle dans le cloud, prêtes à l’emploi dans vos projets de développement.

Avec l’API de traduction de conversation Microsoft Translator Speech, les applications clientes diffusent le contenu audio vocal vers le service, et reçoivent un flux de résultats textuels et audio, dont le texte reconnu dans la langue source et sa traduction dans la langue cible. Les résultats textuels sont générés par l’application au flux audio entrant d’une reconnaissance vocale automatique qui s’appuie sur des réseaux neuronaux profonds. La sortie de la reconnaissance vocale automatique brute est améliorée par une nouvelle technique nommée TrueText permettant de mieux refléter l’intention de l’utilisateur. Par exemple, TrueText élimine les disfluences (euh, toux), les mots répétés et restaure une ponctuation ainsi qu’une casse appropriées. La possibilité de masquer ou d’exclure des vulgarités est également incluse. Les moteurs de reconnaissance et de traduction sont spécifiquement entraînés pour traiter la parole conversationnelle. 

Le service de traduction vocale détecte les silences pour déterminer la fin des énoncés. Après une pause dans l’activité vocale, le service renvoie un résultat final pour l’énoncé complet. Le service peut également renvoyer des résultats partiels, qui donnent des reconnaissances et traductions intermédiaires d’un énoncé en cours. 

Pour la traduction voix-voix, le service permet de convertir par synthèse vocale le texte parlé dans les langues cibles. Le contenu audio de synthèse vocale est créé dans le format spécifié par le client. Les formats disponibles sont WAV et MP3.

L’API Traduction vocale tire parti du protocole WebSocket pour fournir un canal de communication en duplex intégral entre le client et le serveur. 

## <a name="about-microsoft-translator"></a>À propos de Microsoft Translator
Microsoft Translator est un service de traduction informatique. Au cœur de ce service figurent l’API de traduction de texte Translator Text (https://www.microsoft.com/en-us/translator/translatorapi.aspx) et l’API de traduction de conversation Translator Speech qui alimentent différents produits et services Microsoft, que des milliers d’entreprises dans le monde utilisent dans leurs applications et flux de travail pour proposer leur contenu à un public mondial.

Découvrez-en plus sur le [service Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx).

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Traduction automatique neuronale Microsoft Translator
L’API de traduction de conversation Microsoft Translator Speech utilise la traduction automatique statistique héritée et la nouvelle traduction automatique neuronale pour fournir des traductions.

La traduction automatique statistique a atteint un plafond en termes d’amélioration des performances. La qualité de la traduction ne s’améliore plus de manière significative pour les systèmes génériques dotés de la traduction automatique statistique. Une nouvelle technologie de traduction basée sur l’intelligence artificielle et les réseaux neuronaux est en plein essor.

La traduction automatique neuronale fournit de meilleures traductions non seulement en termes de qualité par rapport à une traduction brute, mais également du fait qu’elle donne des résultats plus fluides et humains que la traduction automatique statistique. La raison principale de cette fluidité est que la traduction automatique neuronale utilise le contexte complet d’une phrase pour traduire des mots. La traduction automatique statistique ne prend en compte que le contexte immédiat de quelques mots avant et après chaque mot.

Les modèles de traduction automatique neuronale sont au cœur de l’API et ne sont pas visibles aux utilisateurs finaux. Les seules différences notables sont les suivantes :
* Amélioration de la qualité des traductions, en particulier pour les langues telles que le chinois, le japonais et l’arabe
* Incompatibilité avec les fonctionnalités de personnalisation de hub existantes (pour une utilisation avec l’API de traduction de texte Microsoft Translator Text)

Toutes les langues de traduction vocale prises en charge s’appuient sur la traduction automatique neuronale. Ainsi, la totalité de la traduction voix-voix utilise la traduction automatique neuronale. 

La traduction voix-texte peut combiner traduction automatique neuronale et de traduction automatique statistique en fonction de la paire de langues. Si la langue cible est prise en charge par la traduction automatique neuronale, celle-ci alimente la traduction complète. Si la langue cible n’est pas prise en charge par la traduction automatique neuronale, la traduction combine traduction automatique neuronale et traduction automatique statistique en utilisant l’anglais comme « pivot » entre les deux langues. 

Découvrez les langues prises en charge sur [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Découvrez-en plus sur le [fonctionnement de la traduction automatique neuronale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Inscription](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Commencer à coder](quickstarts/csharp.md)

## <a name="see-also"></a>Voir aussi
- [Page Documentation Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informations sur les solutions et la facturation](https://www.microsoft.com/en-us/translator/home.aspx) 
