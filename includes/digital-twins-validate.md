---
author: baanders
description: Fichier include décrivant comment valider les modèles Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 064f8e322d75e8c92f8ae9d0bd976ebbfb1bdb49
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438568"
---
> [!TIP]
> Après la création d’un modèle, il est recommandé de valider les modèles hors connexion avant de les charger sur une instance Azure Digital Twins.

Il existe un [exemple de validateur DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) indépendant du langage permettant de vérifier que le langage DTDL est correct avant de le charger sur votre instance.

L’exemple de validateur DTDL s’appuie sur une bibliothèque d’analyseur DTDL .NET, disponible sur NuGet comme bibliothèque côté client : [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Vous pouvez également vous servir directement de la bibliothèque pour concevoir votre propre solution de validation. Dans ce cas, veillez à utiliser une version compatible avec celle d’Azure Digital Twins. Actuellement, cette version est *3.12.4*.

Pour plus d’informations sur l’exemple de validateur et la bibliothèque d’analyseur, notamment des exemples d’utilisation, consultez [Analyser et valider les modèles](../articles/digital-twins/how-to-parse-models.md).