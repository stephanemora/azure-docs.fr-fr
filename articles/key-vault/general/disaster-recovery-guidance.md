---
title: Que faire en cas d’interruption d’un service Azure affectant Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Découvrez que faire si une interruption de service Azure affecte Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: sudbalas
ms.openlocfilehash: 4796e6c555ca67794409fb1476f3c4fd0d760719
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780451"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilité et redondance d’Azure Key Vault

Azure Key Vault dispose de plusieurs couches de redondance pour garantir que vos clés et secrets restent disponibles pour votre application même en cas d’échec de composants individuels du service.

Le contenu de votre Key Vault est répliqué dans la région ainsi que dans une région secondaire éloignée d’au moins 241 kilomètres, mais située au sein de la même zone géographique. Cela maintient une durabilité élevée de vos clés et secrets. Consultez le document [Régions jumelées d’Azure](../../best-practices-availability-paired-regions.md) pour plus d’informations sur les paires de régions spécifiques.

En cas d’échec de composants du service Key Vault, d’autres composants de la même région interviennent pour traiter votre demande afin de garantir l’intégrité des fonctionnalités. Il est inutile d’intervenir pour déclencher cette action. Cela se produit automatiquement, sans que vous le perceviez.

Dans les rares cas d’indisponibilité d’une région Azure entière, les demandes d’Azure Key Vault effectuées dans cette région sont automatiquement acheminées (*basculées*) vers une région secondaire. Lorsque la région principale est de nouveau disponible, les demandes sont réacheminées (*basculées*) vers la région principale. Vous n’avez pas besoin d’agir dans la mesure où tout se passe automatiquement.

Dans cette conception haute disponibilité, Azure Key Vault n’exige aucun temps d’arrêt pour les activités de maintenance.

Il convient toutefois de prendre quelques précautions :

* En cas de basculement vers une autre région, quelques minutes peuvent être nécessaires pour que le service soit de nouveau opérationnel. Il se peut que les demandes effectuées pendant cette période échouent jusqu’à ce que le basculement soit terminé.
* Après basculement, votre Key Vault est en lecture seule. Les demandes prises en charge dans ce mode sont les suivantes :
  * Afficher la liste des coffres de clés
  * Obtenir les propriétés des coffres de clés
   * Afficher la liste des certificats
  * Obtenir les certificats
  * Afficher la liste des secrets
  * Obtenir les secrets
  * Afficher la liste des clés
  * Obtenir les (propriétés des) clés
  * Chiffrer
  * Déchiffrer
  * Encapsuler
  * Désencapsuler
  * Vérifier
  * Signer
  * Sauvegarder
* Une fois le basculement restauré, tous les types de demandes (y compris de lecture -read- *et* d’écriture write-) sont disponibles.

