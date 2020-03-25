---
title: Que faire en cas d’interruption d’un service Azure affectant Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Découvrez que faire si une interruption de service Azure affecte Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 530ae2b795b4d94802e9f0d3420f7b3af86936ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184636"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilité et redondance d’Azure Key Vault

Azure Key Vault dispose de plusieurs couches de redondance pour garantir que vos clés et secrets restent disponibles pour votre application même en cas d’échec de composants individuels du service.

Le contenu de votre Key Vault est répliqué dans la région ainsi que dans une région secondaire éloignée d’au moins 241 kilomètres, mais située au sein de la même zone géographique. Cela maintient une durabilité élevée de vos clés et secrets. Consultez le document [Régions jumelées d’Azure](../best-practices-availability-paired-regions.md) pour plus d’informations sur les paires de régions spécifiques.

En cas d’échec de composants du service Key Vault, d’autres composants de la même région interviennent pour traiter votre demande afin de garantir l’intégrité des fonctionnalités. Il est inutile d’intervenir pour déclencher cette action. Cela se produit automatiquement, sans que vous le perceviez.

Dans les rares cas d’indisponibilité d’une région Azure entière, les demandes d’Azure Key Vault effectuées dans cette région sont automatiquement acheminées (*basculées*) vers une région secondaire. Lorsque la région principale est de nouveau disponible, les demandes sont réacheminées (*basculées*) vers la région principale. Vous n’avez pas besoin d’agir dans la mesure où tout se passe automatiquement.

Dans cette conception haute disponibilité, Azure Key Vault n’exige aucun temps d’arrêt pour les activités de maintenance.

Il convient toutefois de prendre quelques précautions :

* En cas de basculement vers une autre région, quelques minutes peuvent être nécessaires pour que le service soit de nouveau opérationnel. Il se peut que les demandes effectuées pendant cette période échouent jusqu’à ce que le basculement soit terminé.
* Après basculement, votre Key Vault est en lecture seule. Les demandes prises en charge dans ce mode sont les suivantes :
  * List key vaults (Afficher la liste des Key Vaults)
  * Get properties of key vaults (Obtenir les propriétés des Key Vaults)
  * List secrets (Afficher la liste des secrets)
  * Get secrets (Obtenir les secrets)
  * List keys (Afficher la liste des clés)
  * Get (properties of) keys (Obtenir les propriétés des clés)
  * Encrypt (Chiffrer)
  * Decrypt (Déchiffrer)
  * Wrap (Encapsuler)
  * Unwrap (Désencapsuler)
  * Vérifier
  * Sign (Signer)
  * Backup
* Une fois le basculement restauré, tous les types de demandes (y compris de lecture -read- *et* d’écriture write-) sont disponibles.

