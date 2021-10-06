---
title: Événements actifs et inactifs - Personalizer
description: Cet article traite de l’utilisation des événements actifs et inactifs au sein du service Personalizer.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219151"
---
# <a name="defer-event-activation"></a>Différer l’activation des événements

L’activation différée des événements vous permet de créer des sites web ou des campagnes de publipostage personnalisés, en partant du principe que l’utilisateur peut ne jamais voir la page ou ouvrir l’e-mail. Dans ces scénarios, l’application peut avoir besoin d’appeler Rank avant même de savoir si le résultat sera utilisé ou affiché pour l’utilisateur. Si le contenu n’est jamais affiché pour l’utilisateur, aucune récompense par défaut (généralement zéro) ne doit être utilisée dans le cadre d’un apprentissage.
L’activation différée vous permet d’utiliser les résultats d’un appel à Rank à un moment donné et de décider si l’événement doit être appris plus tard ou ailleurs dans votre code.

## <a name="typical-scenarios-for-deferred-activation"></a>Scénarios usuels pour l’activation différée

Le report de l’activation des événements est utile dans les exemples de scénarios suivants :

* Vous préaffichez une page web personnalisée pour un utilisateur, mais celui-ci risque de ne jamais la voir, car une logique métier peut remplacer le choix d’action de Personalizer.
* Vous personnalisez du contenu « sous la ligne de flottaison » d’une page web, et il est très possible que ce contenu ne soit jamais vu par l’utilisateur.
* Vous personnalisez des e-mails marketing, et vous devez éviter qu’un entraînement soit effectué sur les e-mails qui n’ont jamais été ouverts par les utilisateurs.
* Vous avez personnalisé une chaîne multimédia dynamique, et vos utilisateurs arrêtent la lecture de la chaîne avant qu’elle n’atteigne les chansons ou vidéos sélectionnées par Personalizer. 

En règle générale, ces scénarios se produisent dans les cas suivants :

* Vous préaffichez une IU que l’utilisateur peut être amené à voir ou non en raison de contraintes liées à l’IU ou au temps.
* Votre application effectue une personnalisation prédictive dans laquelle vous effectuez des appels à Rank avant de savoir si vous allez utiliser la sortie.

## <a name="how-to-defer-activation-and-later-activate-events"></a>Comment différer l’activation des événements et les activer plus tard

Pour différer l’activation d’un événement, appelez [Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) avec `deferActivation = True` dans le corps de requête.

Dès que vous savez que le média ou contenu personnalisé a été présenté aux utilisateurs et qu’il est raisonnable d’attendre une récompense, vous devez activer cet événement. Pour ce faire, appelez l’[API Activate](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) avec eventId.


L’appel de l’[API Activate](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) pour cet EventID doit être reçu avant l’expiration de la fenêtre de temps correspondant à la durée d’attente de la récompense.

### <a name="behavior-with-deferred-activation"></a>Comportement avec activation différée 

Personalizer apprend des événements et des récompenses de la manière suivante :
* Si vous appelez Rank avec `deferActivation = True` et que vous *n’appelez pas* l’API `Activate` pour cet eventId, mais que vous appelez Reward, Personalizer n’apprend pas de l’événement.
* Si vous appelez Rank avec `deferActivation = True`, que vous *appelez* l’API `Activate` pour cet eventId et que vous appelez Reward, Personalizer apprend de l’événement ayant le score de récompense spécifié.
* Si vous appelez Rank avec `deferActivation = True`, que vous *appelez* l’API `Activate` pour cet eventId mais que vous omettez d’appeler Reward, Personalizer apprend de l’événement ayant le score de récompense par défaut défini dans la configuration.

## <a name="next-steps"></a>Étapes suivantes
* Guide pratique pour configurer les [récompenses par défaut](how-to-settings.md#configure-rewards-for-the-feedback-loop).
* Découvrez [comment déterminer le score de récompense et les données à prendre en compte](concept-rewards.md).
