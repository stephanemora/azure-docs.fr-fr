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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177278"
---
Stockage Azure prend en charge le chiffrement au repos avec des clés gérées par Microsoft ou par le client. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.

Utilisez Azure Key Vault pour gérer vos clés et effectuez un audit d’utilisation de votre clé. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../articles/key-vault/key-vault-overview.md)
