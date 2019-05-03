---
title: Limites d’Azure Blockchain
description: Vue d’ensemble du service et les limites fonctionnelles dans Azure Blockchain Service
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028169"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites dans le Service Azure Blockchain

Le Service Azure Blockchain a service et les limites fonctionnelles telles que le nombre de nœuds de qu'un membre peut avoir, les restrictions de consortium et les quantités de stockage.

## <a name="pricing-tier"></a>Niveau tarifaire

Limites maximales sur les transactions et les nœuds du programme de validation varient selon si vous approvisionnez Azure Blockchain Service au base ou des niveaux de tarification Standard.

| Niveau tarifaire | Nœuds de transaction max | Maximal de nœuds du programme de validation |
|:---|:---:|:---:|
| De base | 10 | 1 |
| standard | 10 | 2 |

Modification du niveau tarifaire entre base et Standard après que la création du membre n’est pas pris en charge.

## <a name="storage-capacity"></a>Capacité de stockage

La quantité maximale de stockage qui peut être utilisé par nœud pour les journaux et les données de comptabilité est 1 téraoctet.

Diminution de la taille de stockage de comptabilité et de journal n’est pas pris en charge.

## <a name="consortium-limits"></a>Limites du consortium

* **Noms de membre et Consortium doivent être uniques** à partir d’autres noms de membre et de consortium dans le Service de Blockchain Azure.

* **Les noms de membre et consortium ne peut pas être modifiés**

* **Tous les membres dans un consortium doivent être dans le même niveau tarifaire**

* **Tous les membres qui participent à un consortium doivent résider dans la même région**

    Le premier membre créé dans un consortium dicte la région. Membres invités au consortium doivent résider dans la même région que le premier membre. Limitation de tous les membres de la même région permet de garantir le consensus de réseau n’est pas affecté.

* **Un consortium doit avoir au moins un administrateur**

    Si un consortium n'est qu’un seul administrateur, ils ne peuvent pas se retirer du consortium ou leur membre jusqu'à ce qu’un autre administrateur est ajouté ou promu dans le consortium.

* **Les membres supprimés du consortium ne peut pas être ajoutés à nouveau**

    Au lieu de cela, ils doivent être réinvités à rejoindre le consortium et créer un nouveau membre. Leurs ressources de membres existants ne sont pas supprimés pour conserver l’historique des transactions.

* **Tous les membres dans un consortium doivent être à l’aide de la même version de livre**

    Pour plus d’informations sur les correctifs, mises à jour et du grand livre des versions disponibles dans Azure Blockchain Service, consultez [mise à jour corrective, les mises à jour et les versions](ledger-versions.md).

## <a name="next-steps"></a>Étapes suivantes

* [Mise à jour corrective, les mises à jour et les versions](ledger-versions.md)
