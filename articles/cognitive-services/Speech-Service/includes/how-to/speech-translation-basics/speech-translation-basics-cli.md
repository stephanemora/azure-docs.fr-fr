---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: eric-urban
ms.openlocfilehash: 7600bae5191c5230764b4f75cb93f20049c4b963
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506843"
---
L’une des principales fonctionnalités du service Speech est la possibilité de reconnaître la parole humaine et de la traduire dans d’autres langues. Dans ce démarrage rapide, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Speech dans vos applications et produits afin d’effectuer une traduction vocale de qualité. Ce guide de démarrage rapide convertit la parole du microphone en texte dans une autre langue.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Définir la langue source et la langue cible

Cette commande appelle l’interface CLI de reconnaissance vocale pour convertir la parole du microphone de l’italien au français.

```shell
 spx translate --microphone --source it-IT --target fr
```
