---
title: Présentation de Translator de Microsoft Azure Cognitive Services
titlesuffix: Azure Cognitive Services
description: Intégrez Translator à vos applications, sites web, outils et autres solutions pour offrir une expérience utilisateur multilingue.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/10/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Translator, traduction de texte, traduction automatique, service de traduction, Custom Translator
ms.openlocfilehash: 4d5e60beeb69447823369a5dfe610e73354f1ddc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426955"
---
# <a name="what-is-translator"></a>Présentation de Translator

 Translator est un service de traduction automatique neuronale cloud de la famille d’API REST [Azure Cognitive Services](../what-are-cognitive-services.md).  Compatible avec n’importe quel système d’exploitation, il est à la base de nombreux produits et services Microsoft utilisés par des milliers d’entreprises dans le monde entier pour effectuer des traductions et d’autres opérations linguistiques. Dans cette présentation, vous découvrirez comment Translator peut vous permettre de créer des solutions multilingues intelligentes pour vos applications dans toutes les [langues prises en charge](./language-support.md).

La documentation de Translator contient les types d’articles suivants :

* [**Démarrages rapides**](quickstart-translator.md) : des instructions de prise en main qui vous guident pour effectuer des demandes au service.
* [**Guides pratiques**](translator-how-to-signup.md) : les instructions à suivre pour accéder au service et l’utiliser de manière plus spécifique ou plus personnalisée.
* [**Articles de référence**](reference/v3-0-reference.md) : une documentation sur les API REST et des contenus propres à chaque langage de programmation.

## <a name="translator-features-and-development-options"></a>Fonctionnalités et options de développement de Translator

Les fonctionnalités suivantes sont prises en charge par le service Translator. Suivez les liens figurant dans ce tableau pour en savoir plus sur chaque fonctionnalité et parcourez les documents de référence sur les API.

| Fonctionnalité | Description | Options de développement |
|----------|-------------|--------------------------|
| [**Traduction de texte**](text-translation-overview.md) | Exécutez en temps réel une traduction de texte entre les langues sources et cibles prises en charge. | <ul><li>[**API REST**](reference/rest-api-guide.md) </li><li>[Conteneur Docker Traduction de texte](containers/translator-how-to-install-container.md) (actuellement en préversion soumise à des restrictions d’accès)</li></ul> |
| [**Traduction de documents**](document-translation/overview.md) | Traduisez des fichiers en lots et des fichiers complexes en conservant la structure et le format des documents d’origine. | <ul><li>[**API REST**](document-translation/reference/rest-api-guide.md)</li><li>[**Kit SDK Bibliothèque de client**](document-translation/client-sdks.md)</li></ul> |
| [**Custom Translator**](custom-translator/overview.md) | Créez des modèles personnalisés pour traduire les tournures, la terminologie et le style propres à un domaine et à un secteur d’activité. | <ul><li>[**Portail Custom Translator**](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>Essai gratuit du service Translator

Il vous faut tout d’abord un compte Microsoft. Si vous n’en possédez pas, vous pouvez vous inscrire gratuitement sur le [**portail de compte Microsoft**](https://account.microsoft.com/account).  Sélectionnez **Créer un compte Microsoft** et suivez la procédure pour créer un compte et le vérifier.

Vous avez ensuite besoin d’un compte Azure. Accédez à la [**page d’inscription Azure**](https://azure.microsoft.com/free/ai/), sélectionnez le bouton **Commencer gratuitement**, puis créez un compte Azure avec les informations d’identification de votre compte Microsoft.

Vous pouvez maintenant commencer. [**Créez un service Translator**](translator-how-to-signup.md "Accédez au portail Azure."), [**récupérez vos clés d’accès et votre point de terminaison d’API**](translator-how-to-signup.md#authentication-keys-and-endpoint-url "Une URL de point de terminaison et une clé en lecture seule sont requises pour l’authentification."), et essayez notre [**démarrage rapide**](quickstart-translator.md "Découvrez comment utiliser Translator avec REST.").

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail les fonctionnalités suivantes :
  * [**Traduction de texte**](text-translation-overview.md)
  * [**Traduction de documents**](document-translation/overview.md)
  * [**Custom Translator**](custom-translator/overview.md)
* Consultez la [**tarification de Translator**](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).
