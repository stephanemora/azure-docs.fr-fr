---
title: Récupérer la liste POP Verizon actuelle pour Azure CDN | Microsoft Docs
description: Découvrez comment récupérer la liste POP Verizon actuelle à l’aide de l’API REST.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 9605b352755933b37819527cecbc4e1ccc30e8aa
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579069"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Récupérer la liste POP Verizon actuelle pour Azure CDN

Vous pouvez utiliser l’API REST pour récupérer l’ensemble des adresses IP pour les serveurs POP (point de présence) de Verizon. Ces serveurs POP effectuent des requêtes aux serveurs d’origine qui sont associés à des points de terminaison Azure Content Delivery Network (CDN) sur un profil Verizon (**Azure CDN Standard de Verizon** ou **Azure CDN Premium de Verizon**). Notez que cet ensemble d’adresses IP est différent des adresses IP qu’un client voit lorsqu’ils effectue des requêtes auprès des serveurs POP. 

Pour connaître la syntaxe de l’opération d’API REST pour la récupération de la liste POP, consultez [Nœuds de périphérie - Liste](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Cas d’utilisation classique

Pour des raisons de sécurité, vous pouvez utiliser cette liste IP afin de faire en sorte que les requêtes à votre serveur d’origine sont effectuées uniquement à partir d’un serveur POP Verizon valide. Par exemple, si un utilisateur a découvert le nom d’hôte ou l’adresse IP du serveur d’origine d’un point de terminaison CDN, il peut effectuer des requêtes directement auprès du serveur d’origine, ignorant ainsi les fonctionnalités de mise à l’échelle et de sécurité fournies par Azure CDN. En définissant les adresses IP dans la liste retournée comme les seules adresses IP autorisées sur un serveur d’origine, ce scénario peut être évité. Pour vous assurer que vous avez la dernière liste POP, récupérez-la au moins une fois par jour. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’API REST, consultez [API REST Azure CDN](https://docs.microsoft.com/rest/api/cdn/).