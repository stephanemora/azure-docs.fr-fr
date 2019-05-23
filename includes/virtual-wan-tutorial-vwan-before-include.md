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
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150830"
---
Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Si vous avez déjà un réseau virtuel auquel vous souhaitez vous connecter, vérifiez qu’aucun des sous-réseaux de votre réseau local ne recouvre les réseaux virtuels auxquels vous souhaitez vous connecter. Votre réseau virtuel ne nécessite pas un sous-réseau de passerelles et ne peut pas avoir de passerelles de réseau virtuel. Si vous n’avez pas de réseaux virtuels, vous pouvez en créer un en suivant les étapes dans cet article.
* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel et la plage d’adresses que vous spécifiez pour la région du hub ne peut pas recouvrir les réseaux virtuels existants auxquels vous vous connectez. Il ne peut pas chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, vous devez contacter une personne en mesure de vous aider.
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.