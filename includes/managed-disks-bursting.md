---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a04df7ed283a17ddad6af87cf8215ff8d39a5079
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202564"
---
Le mode rafale est actuellement en préversion pour les disques SSD Premium. Le mode rafale est pris en charge sur toutes les tailles de disques SSD Premium qui sont inférieures ou égales à 512 Gio (P20 ou version inférieure). Ces tailles de disque prennent en charge les rafales sur une base d’effort optimal et utilisent un système de crédit pour gérer celles-ci. Les crédits s’accumulent dans un compartiment de rafales chaque fois que le trafic du disque est inférieur à la cible de performances approvisionnée pour leur taille de disque, et des crédits sont consommés lorsque le trafic va au-delà de la cible. Le suivi du trafic du disque est effectué sur la base des IOPS et de la bande passante dans la cible approvisionnée. Le mode rafale du disque ne contourne pas les limitations de taille des machines virtuelles en matière d'IOPS ou de débit.

Le mode rafale du disque est activé par défaut sur les nouveaux déploiements des tailles de disque qui le prennent en charge. Les tailles de disque existantes, si elles prennent en charge le mode rafale du disque, peuvent activer les rafales via l’une des méthodes suivantes :

- Détachez et rattachez le disque.
- Arrêtez et démarrez la machine virtuelle.

## <a name="burst-states"></a>États de rafale

Toutes les tailles de disque en mode rafale vont commencer par un compartiment de crédits de rafales complet lorsque le disque est attaché à une machine virtuelle. La durée maximale des rafales est déterminée par la taille du compartiment de crédit de rafales. Vous pouvez accumuler uniquement les crédits inutilisés jusqu’à la taille du compartiment de crédits. À tout moment, votre compartiment de crédits de rafales de disque peut avoir l’un des trois états suivants : 

- Accumulation, lorsque le trafic de disque utilise moins que la cible de performances approvisionnée. Vous pouvez accumuler des crédits si le trafic du disque est supérieur aux cibles d’IOPS ou de bande passante ou les deux. Vous pouvez toujours accumuler des crédits d’E/S lorsque vous consommez la bande passante de disque complète, inversement.  

- Diminution, lorsque le trafic de disque utilise plus que la cible de performances approvisionnée. Le trafic en rafale consomme indépendamment les crédits de l’IOPS ou de la bande passante. 

- Reste constant, lorsque le trafic du disque est exactement au niveau de la cible de performances approvisionnée. 

Les tailles de disque qui fournissent une prise en charge des rafales avec les spécifications de rafale sont résumées dans le tableau ci-dessous.

## <a name="regional-availability"></a>Disponibilité régionale

Actuellement, le mode rafale de disque est uniquement disponible dans la région USA Centre-Ouest.

## <a name="disk-sizes"></a>Tailles du disque

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Exemples de scénarios

Pour vous aider à mieux comprendre le fonctionnement, voici quelques exemples de scénarios :

- Un scénario courant pouvant tirer parti du mode rafale de disque est le démarrage des machines virtuelles et le lancement plus rapides des applications sur les disques du système d’exploitation. Prenons comme exemple une machine virtuelle Linux avec une image de système d’exploitation de 8 Gio. Si nous utilisons un disque P2 comme disque de système d’exploitation, la cible approvisionnée est de 120 IOPS et de 25 Mbits/s. Lors du démarrage de la machine virtuelle, il y a un pic de lecture du disque du système d’exploitation chargeant les fichiers de démarrage. Avec l’introduction du mode rafale, vous pouvez lire la vitesse à la vitesse de rafale maximale de 3 500 IOPS et de 170 Mbits/s, accélérant ainsi le temps de chargement d’au moins un facteur 6. Après le démarrage de la machine virtuelle, le niveau de trafic sur le disque du système d’exploitation est généralement faible, car la plupart des opérations de données par l’application sont effectuées sur les disques de données attachés. Si le trafic est inférieur à la cible approvisionnée, vous accumulez les crédits.

- Si vous hébergez un environnement de bureau virtuel distant, chaque fois qu’un utilisateur actif lance une application comme AutoCAD, le trafic de lecture sur le disque du système d’exploitation augmente considérablement. Dans ce cas, le trafic en rafale consomme des crédits accumulés, ce qui vous permet d’aller au-delà de la cible approvisionnée et de lancer l’application beaucoup plus rapidement.

- Un disque P1 a une cible approvisionnée de 120 IOPS et de 25 Mbits/s. Si le trafic réel sur le disque était de 100 IOPS et de 20 Mbits/s au cours du dernier intervalle de 1 seconde, les 20 E/S et les 5 Mo inutilisés sont crédités dans le compartiment de rafales du disque. Les crédits dans le compartiment de rafales peuvent être utilisés ultérieurement lorsque le trafic dépasse la cible approvisionnée, jusqu’à la limite maximale de rafales. La limite maximale de rafales définit le plafond du trafic de disque même si vous disposez de crédits de rafales à consommer. Dans ce cas, même si vous avez 10 000 E/S dans le compartiment de crédits, un disque P1 ne peut pas émettre plus que le nombre maximal de rafales de 3 500 E/S par seconde.  
