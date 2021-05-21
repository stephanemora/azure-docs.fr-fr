---
title: Fichier include
description: Fichier include
services: healthcare-apis
author: stevewohl
ms.service: fhir
ms.topic: include
ms.date: 05/10/2021
ms.author: v-stevewohl
ms.custom: include file
ms.openlocfilehash: 4693edd2392fbb1e813b62346a0ada148445a48f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790118"
---
L’API Azure pour FHIR est une API managée, normalisée et conforme dédiée aux données médicales qui permet d’obtenir des solutions avec une analytique actionnable et le machine learning.

| **Nom du quota** | **Limite par défaut**| **Limite maximale** | **Remarques** |
|---|---|---|---|
|Unités de requête (RU)|10 000 RU|[Contacter le support](https://azure.microsoft.com/support/options/) La valeur maximale disponible est 1 000 000. |Vous avez besoin au minimum de 400 RU ou de 40 RU/Go, selon la valeur la plus grande.|
|Connexions simultanées |15 connexions simultanées sur deux instances (pour un total de 30 demandes simultanées)|[Contacter le support technique](https://azure.microsoft.com/support/options/)||
|Instances du service API Azure pour FHIR par abonnement|10|[Contacter le support technique](https://azure.microsoft.com/support/options/)||