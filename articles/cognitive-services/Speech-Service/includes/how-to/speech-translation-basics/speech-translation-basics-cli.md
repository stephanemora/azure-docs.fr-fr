---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776650"
---
L’une des principales fonctionnalités du service Speech est la possibilité de reconnaître la parole humaine et de la traduire dans d’autres langues. Dans ce démarrage rapide, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Speech dans vos applications et produits afin d’effectuer une traduction vocale de qualité. Ce guide de démarrage rapide convertit la parole du microphone en texte dans une autre langue.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Définir la langue source et la langue cible

Cette commande appelle l’interface CLI de reconnaissance vocale pour convertir la parole du microphone de l’italien au français.

```shell
 spx translate --microphone --source it-IT --target fr
```
