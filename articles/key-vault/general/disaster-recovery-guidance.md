---
title: Que faire si une interruption de service Azure affecte Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Découvrez comment réagir si une interruption de service Azure affecte Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 35814f34550ac7bf4ad85a96d0838df62fe63be6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073180"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilité et redondance d’Azure Key Vault

Azure Key Vault dispose de plusieurs couches de redondance pour garantir que vos clés et secrets restent disponibles pour votre application même en cas d’échec de composants individuels du service.

Le contenu de votre coffre de clés est répliqué dans la région ainsi que dans une région secondaire éloignée d’au moins 240 km, mais située au sein de la même zone géographique, afin de maintenir une durabilité élevée de vos clés et secrets. Consultez le document [Régions jumelées d’Azure](../../best-practices-availability-paired-regions.md) pour plus d’informations sur les paires de régions spécifiques.

En cas d’échec de composants du service Key Vault, d’autres composants de la même région interviennent pour traiter votre demande afin de garantir l’intégrité des fonctionnalités. Vous n’avez besoin d’effectuer aucune action pour démarrer ce processus. Il se produit automatiquement sans que vous le perceviez.

Dans les rares cas d’indisponibilité d’une région Azure entière, les demandes d’Azure Key Vault effectuées dans cette région sont automatiquement acheminées (*basculées*) vers une région secondaire. Lorsque la région principale est de nouveau disponible, les demandes sont réacheminées (*basculées*) vers la région principale. Vous n’avez pas besoin d’agir dans la mesure où tout se passe automatiquement.

Dans cette conception haute disponibilité, Azure Key Vault n’exige aucun temps d’arrêt pour les activités de maintenance.

Il convient toutefois de prendre quelques précautions :

* En cas de basculement vers une autre région, quelques minutes peuvent être nécessaires pour que le service soit de nouveau opérationnel. Les demandes effectuées pendant cette période, avant le basculement, peuvent échouer.
* Au cours du basculement, votre coffre de clés est en lecture seule. Les demandes prises en charge dans ce mode sont les suivantes :
  * Afficher la liste des certificats
  * Obtenir les certificats
  * Afficher la liste des secrets
  * Get secrets (Obtenir les secrets)
  * Afficher la liste des clés
  * Obtenir les (propriétés des) clés
  * Encrypt (Chiffrer)
  * Déchiffrer
  * Encapsuler
  * Désencapsuler
  * Vérifier
  * Signer
  * Backup
* Au cours du basculement, vous ne pouvez pas apporter de modifications aux propriétés du coffre de clés. Vous ne serez pas en mesure de modifier la configuration ni les paramètres de la stratégie d’accès et du pare-feu.
* Une fois le basculement restauré, tous les types de demandes (y compris de lecture -read- *et* d’écriture write-) sont disponibles.

