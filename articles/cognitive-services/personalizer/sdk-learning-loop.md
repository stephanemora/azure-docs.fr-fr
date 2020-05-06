---
title: 'Démarrage rapide : Créer et utiliser une boucle d’apprentissage avec le SDK - Personalizer'
description: Ce guide de démarrage rapide vous montre comment créer et gérer votre base de connaissances à l’aide du SDK client.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: c65be6c68372ffe92ffd942bf71d2b635ec2fd16
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188854"
---
# <a name="quickstart-personalizer-client-library"></a>Démarrage rapide : Bibliothèque de client Personalizer

Suivez ce guide de démarrage rapide pour afficher du contenu personnalisé avec le service Personalizer.

Commencez à utiliser la bibliothèque de client Personalizer. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.

 * API Rank : sélectionne l’élément le mieux adapté parmi les actions en fonction des informations en temps réel que vous fournissez sur le contenu et le contexte.
 * API Reward : vous déterminez le score de récompense en fonction des besoins de votre entreprise, puis vous l’envoyez à Personalizer avec cette API. Ce score peut être une valeur unique comme 1 pour Bon et 0 pour Mauvais, ou un algorithme que vous créez en fonction des besoins de votre entreprise.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Fonctionnement de Personalizer](how-personalizer-works.md)

* [Qu’est-ce que Personalizer ?](what-is-personalizer.md)
* [Où utiliser Personalizer ?](where-can-you-use-personalizer.md)
* [Dépannage](troubleshooting.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
