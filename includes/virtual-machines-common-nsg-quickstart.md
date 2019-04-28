---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows
author: rockboyfor
ms.service: virtual-machines-windows
ms.topic: include
origin.date: 09/12/2018
ms.date: 11/12/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405379"
---
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic.

L'exemple de cet article montre comment créer un filtre réseau qui utilise le port TCP standard 80 (en supposant que vous avez déjà lancé les services appropriés et ouvert toutes les règles de pare-feu du système d'exploitation sur la machine virtuelle).

Une fois que vous avez créé une machine virtuelle configurée pour traiter des requêtes web sur le port TCP 80 standard, vous pouvez :

1. Créer des groupes de sécurité réseau

2. Créer une règle de sécurité de trafic entrant autorisant le trafic et affecter des valeurs aux paramètres suivants :

   - **Plages de ports de destination** : 80

   - **Plages de ports sources** : * (autorise n’importe quel port source)

   - **Valeur de priorité**: Entrez une valeur inférieure à 65 500 et supérieure dans la priorité à la valeur par défaut pour tout refuser la règle de trafic entrant.

3. Associer le groupe de sécurité réseau à l’interface réseau de machine virtuelle ou au sous-réseau.

    Bien que cet exemple utilise une règle simple pour autoriser le trafic HTTP, vous pouvez également utiliser des groupes de sécurité réseau et des règles pour créer des configurations réseau plus complexes.