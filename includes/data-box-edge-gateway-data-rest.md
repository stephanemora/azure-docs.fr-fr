---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120555"
---
Pour les données au repos :

- Le chiffrement BitLocker XTS-AES de 256 bits est utilisé pour protéger les données locales.
- L’accès aux données stockées dans des partages est limité.

    - Les clients SMB qui accèdent aux données partagées ont besoin des informations d’identification utilisateur associées au partage. Ces informations d’identification sont définies lorsque le partage est créé.
    - Les adresses IP des clients NFS qui accèdent à un partage doivent être ajoutées lorsque le partage est créé.