---
title: Ne validez pas les jetons d’accès pour Microsoft Graph
description: Incluez un avertissement de fichier indiquant que les jetons d’accès pour Microsoft Graph doivent être considérés comme étant opaques et ne doivent jamais être validés par le code client. Seul Microsoft Graph valide les jetons d’accès Microsoft Graph.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e3dad40f1e4c1e8c463217720a190fd0e961334f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909743"
---
> [!WARNING]
> N’essayez pas de valider ou de lire des jetons pour des API dont vous n’êtes pas propriétaire, y compris les jetons de cet exemple, dans votre code. Les jetons associés aux services Microsoft peuvent utiliser un format spécial qui n’est pas validé comme jeton JWT, et peuvent également être chiffrés pour les utilisateurs consommateurs (de comptes Microsoft). Même si la lecture des jetons est un outil utile de débogage et d’apprentissage, n’y associez pas de dépendances dans votre code ou tenez compte des spécificités des jetons qui ne correspondent pas à une API que vous contrôlez.
