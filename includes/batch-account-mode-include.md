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
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127561"
---
> [!NOTE]
> Lorsque vous créez un compte Batch, vous pouvez choisir entre deux modes de *répartition de pool* : **abonnement utilisateur** et **service Batch**. Dans la plupart des cas, vous devez utiliser le mode de service Batch par défaut. Les pools sont alloués en arrière-plan dans des abonnements gérés par Azure. Dans l’autre mode d’abonnement utilisateur, les machines virtuelles Batch et les autres ressources sont créées directement dans l’abonnement lors de la création d’un pool. Le mode d’abonnement utilisateur est nécessaire si vous voulez créer des pools Batch à l’aide d’[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Pour créer un compte Batch dans le mode abonnement utilisateur, vous devez également inscrire votre abonnement auprès d’Azure Batch et associer le compte avec une Azure Key Vault.