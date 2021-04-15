---
title: Fichier include
description: fichier
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92283077"
---
Si vous avez besoin d’une configuration supplémentaire, utilisez une tâche planifiée exécutée au démarrage pour apporter des modifications finales à la machine virtuelle après sont déploiement. Tenez également compte des éléments suivants :

- S’il s’agit d’une tâche à exécution unique, la tâche doit se supprimer une fois qu’elle s’est terminée avec succès.
- Les configurations ne doivent pas s’appuyer sur des lecteurs autres que C ou D, car seuls ces deux lecteurs sont toujours présents (le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire).

Pour plus d’informations sur les personnalisations Linux, consultez l’article [Extensions et fonctionnalités de machine virtuelle pour Linux](../../virtual-machines/extensions/features-linux.md).