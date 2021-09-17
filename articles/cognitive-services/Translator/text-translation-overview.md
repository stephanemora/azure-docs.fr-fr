---
title: Qu’est-ce que la Traduction de texte Microsoft Azure Cognitive Services ?
titlesuffix: Azure Cognitive Services
description: Intégrez l’API de traduction de texte Traduction de texte à vos applications, sites web, outils et autres solutions, pour offrir une expérience utilisateur multilingue.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: traducteur, traduction de texte, traduction automatique, service de traduction, traducteur personnalisé
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785226"
---
# <a name="what-is-text-translation"></a>Qu’est-ce que la traduction de texte ?

 La traduction de texte est une fonctionnalité d’API REST basée sur le cloud du service Traducteur qui utilise la technologie de traduction automatique neuronale pour permettre une traduction de texte source en temps réel rapide et précise dans toutes les [langues prises en charge](language-support.md). Dans cette présentation, vous apprendrez comment les API REST Traduction de texte vous permettent de créer des solutions intelligentes pour vos applications et vos flux de travail.

Cette traduction de documentation contient les types d’articles suivants :

* [**Démarrages rapides**](quickstart-translator.md). Des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* [**Guides pratiques**](translator-how-to-signup.md). Des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* [**Articles de référence**](reference/v3-0-reference.md). Documentation sur l’API REST et contenu basé sur le langage de programmation.

## <a name="text-translation-features"></a>Fonctionnalités de traduction de texte

 La traduction de texte prend en charge les méthodes suivantes :

* [**Langues**](reference/v3-0-languages.md). Retourne une liste des langues prises en charge par les opérations **Traduire**, **Translittérer** et **Rechercher dans le dictionnaire**. Cette requête ne nécessite pas d’authentification. Il vous suffit de copier et coller la requête GET suivante dans Postman ou votre navigateur ou outil d’API favori :

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [**Traduire**](reference/v3-0-translate.md#translate-to-multiple-languages). Restitue un texte en langage source unique dans plusieurs textes de langue cible avec une seule requête.

* [**Translittérer**](reference/v3-0-transliterate.md). Convertit des caractères ou des lettres d’une langue source en caractères ou lettres correspondants d’une langue cible.

* [**Détecter**](reference/v3-0-detect.md). Retourne la langue source et une variable booléenne indiquant si la langue détectée est prise en charge pour la traduction et la translittération de texte.

    > [!NOTE]
    > Vous pouvez **traduire, translittérer et détecter** le texte avec [un seul appel d’API REST](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection).

* [**Recherche dans le dictionnaire**](reference/v3-0-dictionary-lookup.md). Retourne des mots équivalents pour le terme source dans la langue cible.
* [**Exemple de dictionnaire**](reference/v3-0-dictionary-examples.md) Retourne une structure grammaticale et des exemples de contexte pour la paire terme source et terme cible.

## <a name="text-translation-deployment-options"></a>Options de déploiement de la traduction de texte

Ajoutez la traduction de texte à vos projets et applications à l’aide des ressources suivantes :

* Accédez au service Traducteur cloud via l’[**API REST**](reference/rest-api-guide.md), disponible dans Azure.

* Utilisez la [requête translate](containers/translator-container-supported-parameters.md) de l’API REST avec le [**conteneur Docker de traduction de texte**](containers/translator-how-to-install-container.md).

    > [!IMPORTANT]
    >
    > * Le conteneur Traducteur est en préversion contrôlée. Pour l’utiliser, vous devez compléter et envoyer la [**Demande Azure Cognitive Services pour les services contrôlés**](https://aka.ms/csgate-translator) et la faire approuver pour obtenir l’accès au conteneur.
    >
    > * L’[**image conteneur Translator**](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation) prend en charge des fonctionnalités limitées par rapport aux offres cloud.
    >

## <a name="get-started-with-text-translation"></a>Bien démarrer avec la traduction de texte

Vous êtes prêt à commencer ?

* [**Créer une ressource Traducteur**](translator-how-to-signup.md "Accédez au portail Azure.") dans le portail Azure.

* [**Récupérez vos clés d’accès et votre point de terminaison d’API**](translator-how-to-signup.md#authentication-keys-and-endpoint-url). Une URL de point de terminaison et une clé en lecture seule sont requises pour l’authentification.

* Explorez notre [**démarrage rapide**](quickstart-translator.md "Apprenez à utiliser le Traducteur par le biais de REST et de votre langage de programmation préféré.") et consultez les cas d’utilisation et les exemples de code pour les langages de programmation suivants : 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>Étapes suivantes

Explorez plus en détail l’API REST de traduction de texte :

> [!div class="nextstepaction"]
> [Voir la référence de l'API REST](./reference/v3-0-reference.md)
