---
title: Traduire derrière des pare-feu - Translator
titleSuffix: Azure Cognitive Services
description: Le service Translator d’Azure Cognitive Services peut traduire derrière des pare-feu utilisant le filtrage de noms de domaine ou d’adresses IP.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401861"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Comment traduire derrière des pare-feu IP avec le service Translator

Le service Translator peut traduire derrière des pare-feu utilisant le filtrage de noms de domaine ou d’adresses IP. Le filtrage de noms de domaine est la méthode préférée. Si vous avez toujours besoin d’un filtrage IP, nous vous suggérons d’obtenir les [détails des adresses IP à l’aide de l’étiquette de service](../../virtual-network/service-tags-overview.md#service-tags-on-premises). Traducteur se trouve sous l’étiquette de service« CognitiveServicesManagement ». 

Nous vous **déconseillons** d’exécuter Traducteur Microsoft derrière un pare-feu à filtre IP spécifique. Cette configuration est susceptible de planter à tout moment.

Les adresses IP des points de terminaison géographiques de Traducteur à partir du 21 septembre 2021 sont les suivantes :

|Geography|URL de base (point de terminaison géographique)|Adresses IP|
|:--|:--|:--|
|États-Unis|api-nam.cognitive.microsofttranslator.com|20.42.6.144, 20.49.96.128, 40.80.190.224, 40.64.128.192|
|Europe|api-eur.cognitive.microsofttranslator.com|20.50.1.16, 20.38.87.129|
|Asie-Pacifique|api-apc.cognitive.microsofttranslator.com|40.80.170.160, 20.43.132.96, 20.37.196.160, 20.43.66.16|
