---
title: Limites d’Azure Blockchain
description: Vue d’ensemble des limites de service et fonctionnelles dans Azure Blockchain Service
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: aeed84f19da3843d043eafef9d7444661901c53e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147143"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites dans Azure Blockchain Service

Azure Blockchain Service présente des limites de service et fonctionnelles telles que le nombre de nœuds qu’un membre peut avoir, des restrictions de consortium et des quantités de stockage.

## <a name="pricing-tier"></a>Niveau tarifaire

Les limites maximales sur les transactions et les nœuds validateurs varient selon que vous approvisionnez Azure Blockchain Service avec le niveau tarifaire De base ou Standard.

| Niveau tarifaire | Nombre maximal de nœuds de transaction | Nombre maximal de nœuds validateurs |
|:---|:---:|:---:|
| De base | 10 | 1 |
| standard | 10 | 2 |

La modification du niveau tarifaire entre le niveau De base et le niveau Standard après la création du membre n’est pas prise en charge.

## <a name="storage-capacity"></a>Capacité de stockage

La quantité de stockage maximale qui peut être utilisée par nœud pour les journaux et données de registre est 1.8 To.

La diminution de la taille de stockage des registres et journaux n’est pas prise en charge.

## <a name="consortium-limits"></a>Limites de consortium

* **Les noms de membre et de consortium doivent être uniques** et différents des autres noms de membre et de consortium dans Azure Blockchain Service.

* **Les noms de membre et de consortium ne peuvent pas être modifiés**

* **Tous les membres d’un consortium doivent s’inscrire dans le même niveau tarifaire**

* **Tous les membres qui participent à un consortium doivent résider dans la même région**

    Le premier membre créé dans un consortium détermine la région. Les membres invités au consortium doivent résider dans la même région que le premier membre. La limitation de tous les membres à la même région permet de garantir que le consensus du réseau n’est pas affecté.

* **Un consortium doit avoir au moins un administrateur**

    Si un consortium n’a qu’un administrateur, celui-ci ne peut pas se retirer du consortium ou supprimer ses membres tant qu’un autre administrateur n’est pas ajouté ou promu dans le consortium.

* **Les membres supprimés du consortium ne peuvent pas être ajoutés à nouveau**

    Au lieu de cela, ils doivent être réinvités à rejoindre le consortium et créer un nouveau membre. Leurs ressources de membre existantes ne sont pas supprimées, de sorte que les transactions historiques soient conservées.

* **Tous les membres d’un consortium doivent utiliser la même version de registre**

    Pour plus d’informations sur l’application de correctifs, les mises à jour et les versions de registre disponibles dans Azure Blockchain Service, consultez [Patching, updates, and versions](ledger-versions.md) (Application de correctifs, mises à jour et versions).

## <a name="next-steps"></a>Étapes suivantes

* [Patching, updates, and versions](ledger-versions.md) (Application de correctifs, mises à jour et versions).
