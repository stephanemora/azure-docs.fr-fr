---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638135"
---
La région et la clé de l’abonnement aux ressources Speech sont nécessaires pour créer un objet de configuration Speech. L’objet de configuration est nécessaire pour instancier un objet de reconnaissance vocale.

Le module de reconnaissance vocale expose plusieurs façons de reconnaître la parole. Dans cet exemple, la parole est reconnue une fois. Cette fonctionnalité permet au service Speech de savoir que vous envoyez une seule phrase pour reconnaissance, et qu’il peut arrêter la reconnaissance une fois la phrase identifiée. Lorsque le résultat est généré, le code écrit le produit de la reconnaissance dans la console.

> [!TIP]
> Le kit SDK Speech utilise `en-us` comme langue par défaut pour la reconnaissance. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../how-to-specify-source-language.md) pour en savoir plus sur le choix de la langue source.