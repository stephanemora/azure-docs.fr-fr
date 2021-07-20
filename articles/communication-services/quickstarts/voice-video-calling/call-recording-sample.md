---
title: Vue d’ensemble de l’API d’enregistrement d’appel Azure Communication Services
titleSuffix: An Azure Communication Services quickstart document
description: Fournit un exemple de démarrage rapide pour les API d’automatisation des appels.
author: ravithanneeru
manager: joseys
services: azure-communication-services
ms.author: joseys
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: f4dfd0701c2eeeaa5ba3b2be20b9448ca2093601
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111797"
---
# <a name="call-recording-api-quickstart"></a>Démarrage rapide de l’API d’enregistrement d’appel
Ce démarrage rapide vous mntre comment enregistrer des appels vocaux et vidéo. Ce démarrage rapide suppose que vous avez déjà utilisé le [Kit de développement logiciel (SDK) Calling](get-started-with-video-calling.md) pour créer l’expérience d’appel d’utilisateur final. À l’aide des **API et kits de développement logiciel (SDK) d’appel du serveur**, vous pouvez activer et gérer les enregistrements. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build Call Recording server sample with C#](./includes/call-recording-samples/recording-server-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build Call Recording server sample with Java](./includes/call-recording-samples/recording-server-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Consultez notre [exemple de bannière d’appel](../../samples/calling-hero-sample.md).
- Apprenez-en davantage sur les [fonctionnalités du SDK Calling](./calling-client-samples.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../../concepts/voice-video-calling/about-call-types.md)
