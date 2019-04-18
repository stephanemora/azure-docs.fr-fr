---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684411"
---
Pour les données en transit :

- Pour les données qui sont placées entre l’appareil et Azure, standard TLS 1.2 est utilisé. Il n’existe aucun élément de secours à TLS 1.1 et versions antérieures. La communication de l’agent sera bloquée si TLS 1.2 n’est pas pris en charge. Le TLS 1.2 est également requise pour portail et les opérations de gestion de kit de développement logiciel.
- Lorsque les clients accèdent à votre appareil via l’interface utilisateur web locale dans un navigateur, standard TLS 1.2 est utilisé comme le protocole sécurisé par défaut.

    - La meilleure pratique consiste à configurer votre navigateur pour utiliser TLS 1.2.
    - Si le navigateur ne prend pas en charge TLS 1.2, vous pouvez utiliser TLS 1.1 ou TLS 1.0.
- Pour protéger les données lorsque vous la copiez à partir de vos serveurs de données, nous vous recommandons d’utiliser SMB 3.0 avec chiffrement.
