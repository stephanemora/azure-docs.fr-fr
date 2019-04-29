---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754286"
---
Pour les données en vol :

- Standard TLS 1.2 est utilisé pour les données qui transitent entre le périphérique et Azure. Il n’existe aucun élément de secours à TLS 1.1 et versions antérieures. Communication de l’agent sera bloquée si TLS 1.2 n’est pas pris en charge. TLS 1.2 est également requise pour gestion de kit de développement logiciel et de portail.
- Lorsque les clients accèdent à votre appareil via le web locale de l’interface utilisateur d’un navigateur, standard TLS 1.2 est utilisé comme le protocole sécurisé par défaut.

    - La meilleure pratique consiste à configurer votre navigateur pour utiliser TLS 1.2.
    - Si le navigateur ne prend pas en charge TLS 1.2, vous pouvez utiliser TLS 1.1 ou TLS 1.0.
- Nous vous recommandons d’utiliser SMB 3.0 avec chiffrement pour protéger les données lorsque vous la copiez à partir de vos serveurs de données.
