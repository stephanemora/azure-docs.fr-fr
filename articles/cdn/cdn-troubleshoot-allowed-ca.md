---
title: Autorité de certification autorisée pour le protocole HTTPS personnalisé
titleSuffix: Azure Content Delivery Network
description: Si vous utilisez votre propre certificat pour activer HTTPS sur un domaine personnalisé, vous devrez utiliser une autorité de certification autorisée (CA) pour le créer.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003607"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Autorités de certification autorisées pour le protocole HTTPS personnalisé

Lorsque vous [activez la fonctionnalité HTTPS à l’aide de votre propre certificat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) pour un domaine personnalisé Azure Content Delivery Network (CDN), des exigences de certificat spécifiques sont requises. 

* Le profil **Azure CDN Standard de Microsoft** requiert un certificat de l’une des autorités de certification approuvées répertoriées dans la liste suivante. En cas d’utilisation d’un certificat d’une autorité de certification non approuvée ou d’un certificat auto-signé, la demande est rejetée. 

* Les profils **Azure CDN Standard de Verizon** et **Azure CDN Premium de Verizon** acceptent tout certificat valide de toute autorité de certification. Les profils Verizon ne prennent pas en charge les certificats auto-signés.

> [!NOTE]
> L’option permettant d’utiliser votre propre certificat pour activer la fonctionnalité HTTPS de domaine personnalisé n’est *pas* disponible pour des profils **Azure CDN Standard d’Akamai**. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

