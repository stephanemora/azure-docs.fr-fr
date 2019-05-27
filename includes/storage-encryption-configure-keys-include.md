---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115443"
---
Stockage Azure prend en charge le chiffrement au repos avec clés gérées par Microsoft ou de clés gérées par le client. Clés gérées par le client permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.

Utilisez Azure Key Vault pour gérer vos clés et d’audit de votre utilisation de la clé. Vous pouvez soit créer vos propres clés et les stocker dans un coffre de clés, ou vous pouvez utiliser les API Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../articles/key-vault/key-vault-overview.md)
