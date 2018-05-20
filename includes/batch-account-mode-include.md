---
title: Fichier Include
description: Fichier Include
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
---
> [!NOTE]
> Lorsque vous créez un compte Batch, vous pouvez choisir entre deux modes de *répartition de pool* : **abonnement utilisateur** et **service Batch**. Dans la plupart des cas, vous devez utiliser le mode de service Batch par défaut. Les pools sont alloués en arrière-plan dans des abonnements gérés par Azure. Dans l’autre mode d’abonnement utilisateur, les machines virtuelles Batch et les autres ressources sont créées directement dans l’abonnement lors de la création d’un pool. Le mode d’abonnement utilisateur est nécessaire si vous voulez créer des pools Batch à l’aide d’[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Pour créer un compte Batch dans le mode abonnement utilisateur, vous devez également inscrire votre abonnement auprès d’Azure Batch et associer le compte avec une Azure Key Vault.