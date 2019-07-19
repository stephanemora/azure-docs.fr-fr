---
title: Modèle de déploiement Azure Classic | Microsoft Docs
description: Modèle de déploiement classique Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313217"
---
# <a name="classic-deployment-model"></a>Modèle de déploiement Classic

Le modèle de déploiement Classic est le modèle de déploiement Azure d’ancienne génération et impose une limite de quota de processeurs virtuels globale pour les machines virtuelles et les groupes de machines virtuelles identiques. Le modèle de déploiement Classic n’est plus recommandé et est maintenant remplacé par le modèle Resource Manager. Pour en savoir plus sur ces deux modèles de déploiement et sur l’avantage de Resource Manager, consultez la page consacrée au modèle de déploiement de Resource Manager. Quand un abonnement est créé, un quota de processeurs virtuels par défaut lui est attribué. Chaque fois qu’une nouvelle machine virtuelle doit être déployée à l’aide du modèle de déploiement Classic, la somme de l’utilisation des processeurs virtuels nouveaux et existants dans toutes les régions ne doit pas dépasser le quota de processeurs virtuels approuvé pour le modèle de déploiement Classic. Pour en savoir plus sur les quotas, consultez la page Abonnement Azure et limites de service.

Vous pouvez demander une augmentation de la limite de processeurs virtuels pour le modèle de déploiement Classic par le biais du panneau Aide + Support ou du panneau Utilisations + Quota dans le portail.

