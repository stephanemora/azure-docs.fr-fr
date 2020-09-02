---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8833639b6efacc664596ecb2aa6f9da41ad23b81
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814131"
---
- Seules les [clés RSA HSM et de logiciels](../articles/key-vault/keys/about-keys.md) des tailles une tailles 2 048 bits, 3 072 bits et 4 096 bits sont prises en charge ; aucune autre clé ou taille n’est prise en charge.
    - Les clés [HSM](../articles/key-vault/keys/hsm-protected-keys.md) nécessitent le niveau **Premium** de coffres de clés Azure.
- Les disques créés à partir d’images personnalisées et chiffrées à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés à l’aide des mêmes clés gérées par le client et doivent figurer dans le même abonnement.
- Les instantanés créés à partir de disques chiffrés à l’aide du chiffrement côté serveur et des clés gérées par le client doivent être chiffrés avec les mêmes clés gérées par le client.
- Toutes les ressources liées à vos clés gérées par le client (coffres de clés Azure, jeux de chiffrement de disque, machines virtuelles, disques et instantanés) doivent se trouver dans le même abonnement et la même région.
- Les disques, les instantanés et les images chiffrées avec des clés gérées par le client ne peuvent pas être déplacés vers un autre abonnement.
- Les disques managés actuellement ou précédemment chiffrés à l'aide d'Azure Disk Encryption ne peuvent pas être chiffrés avec des clés gérées par le client.
- Ne peut créer que jusqu’à 50 jeux de chiffrements de disques par région et par abonnement.
- Pour plus d’informations sur l’utilisation de clés gérées par le client avec des galeries d’images partagées, consultez [Préversion : Utiliser des clés gérées par le client pour le chiffrement d’images](../articles/virtual-machines/image-version-encryption.md).