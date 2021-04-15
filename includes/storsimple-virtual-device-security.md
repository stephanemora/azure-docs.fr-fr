---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67177107"
---
<!--v-sharos 10/13/2105 virtual device security-->

Gardez à l’esprit les considérations de sécurité suivantes lorsque vous utilisez l’appareil virtuel StorSimple :

* L’appareil virtuel est sécurisé via votre abonnement Microsoft Azure. Cela signifie que si vous utilisez l’appareil virtuel et que votre abonnement Azure est compromis, les données stockées sur votre appareil virtuel sont également vulnérables.
* La clé publique du certificat permettant de chiffrer les données stockées dans Azure StorSimple est accessible en toute sécurité dans le Portail Azure Classic, et la clé privée est conservée avec l’appareil StorSimple. Sur l’appareil virtuel StorSimple, les clés publiques et privées sont stockées dans Azure.
* L’appareil virtuel est hébergé dans le centre de données Microsoft Azure.

