---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665902"
---
Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour un plus grand contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client au niveau du compte de stockage.

Les clés gérées par le client doivent être stockées dans Azure Key Vault. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur le chiffrement et la gestion des clés dans le stockage Azure, consultez [Chiffrement du stockage Azure pour les données au repos](../articles/storage/common/storage-service-encryption.md). Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../articles/key-vault/key-vault-overview.md)
