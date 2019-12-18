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
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895289"
---
Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données de fichiers et d’objets blob.

Les clés gérées par le client doivent être stockées dans Azure Key Vault. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur le chiffrement et la gestion des clés dans le stockage Azure, consultez [Chiffrement du stockage Azure pour les données au repos](../articles/storage/common/storage-service-encryption.md). Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../articles/key-vault/key-vault-overview.md)
