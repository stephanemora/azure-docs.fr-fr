---
author: baanders
description: Fichier include décrivant comment valider les modèles Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130377"
---
> [!TIP]
> Après la création d’un modèle, il est recommandé de valider les modèles hors connexion avant de les charger sur une instance Azure Digital Twins.

Il existe un exemple de validation de documents de modèle indépendant du langage permettant de vérifier que le langage DTDL est correct avant de le charger sur votre instance. Il se trouve ici : [**Exemple de validateur DTDL**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* L’exemple de validateur DTDL s’appuie sur une bibliothèque d’analyseur DTDL .NET, disponible sur NuGet comme bibliothèque côté client : [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Vous pouvez également vous servir directement de la bibliothèque pour concevoir votre propre solution de validation. Dans ce cas, veillez à utiliser une version compatible avec celle d’Azure Digital Twins. Actuellement, cette version est *3.12.4*.

Pour plus d’informations sur l’exemple de validateur et la bibliothèque d’analyseur, notamment des exemples d’utilisation, consultez [*Guide pratique pour analyser et valider les modèles*](../articles/digital-twins/how-to-parse-models.md).