---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754285"
---
Pour les données au repos :

- Chiffrement de BitLocker XTS-AES 256 bits est utilisé pour protéger les données locales.
- Accès aux données stockées dans des partages est limité.

    - Les clients SMB qui accèdent aux données de partage besoin des informations d’identification de l’utilisateur associées au partage. Ces informations d’identification sont définies lorsque le partage est créé.
    - Les adresses IP des clients NFS qui accèdent à un partage doivent être ajoutés lorsque le partage est créé.