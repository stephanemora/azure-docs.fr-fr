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
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432058"
---
> [!WARNING]
> N’essayez pas de valider ou de lire des jetons pour des API dont vous n’êtes pas propriétaire, y compris les jetons de cet exemple, dans votre code.  Les jetons associés aux services Microsoft peuvent se présenter sou un format spécial qui n’est pas validé en tant que JWT, et peuvent également être chiffrés pour les utilisateurs du consommateur (compte Microsoft). Même si la lecture des jetons est un outil utile de débogage et d’apprentissage, n’y associez pas de dépendances dans votre code ou tenez compte des spécificités des jetons qui ne correspondent pas à une API que vous contrôlez.
