---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 09f642a4c96781276d225cba37d71386d429d0c9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004133"
---
Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Si vous avez déjà un réseau virtuel auquel vous souhaitez vous connecter, vérifiez qu’aucun des sous-réseaux de votre réseau local ne recouvre les réseaux virtuels auxquels vous souhaitez vous connecter. Votre réseau virtuel ne nécessite pas un sous-réseau de passerelles et ne peut pas avoir de passerelles de réseau virtuel. Si vous n’avez pas de réseaux virtuels, vous pouvez en créer un en suivant les étapes dans cet article.
* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel et la plage d’adresses que vous spécifiez pour la région du hub ne peut pas recouvrir les réseaux virtuels existants auxquels vous vous connectez. Il ne peut pas chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, vous devez contacter une personne en mesure de vous aider.
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.