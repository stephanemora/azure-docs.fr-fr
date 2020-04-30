---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400762"
---
La région et la clé de l’abonnement aux ressources Speech sont nécessaires pour créer un objet de configuration Speech. L’objet de configuration est nécessaire pour instancier un objet de reconnaissance vocale.

Le module de reconnaissance vocale expose plusieurs façons de reconnaître la parole. Dans cet exemple, la parole est reconnue une fois. Cette fonctionnalité permet au service Speech de savoir que vous envoyez une seule phrase pour reconnaissance, et qu’il peut arrêter la reconnaissance une fois la phrase identifiée. Lorsque le résultat est généré, le code écrit le produit de la reconnaissance dans la console.

> [!TIP]
> Le kit SDK Speech utilise `en-us` comme langue par défaut pour la reconnaissance. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../how-to-specify-source-language.md) pour en savoir plus sur le choix de la langue source.