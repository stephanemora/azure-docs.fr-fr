---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684427"
---
Pour le données au repos :

- Pour le données au repos, le chiffrement BitLocker XTS AES-256 est utilisé pour protéger les données locales.
- Pour les données qui résident dans des partages, l’accès aux partages est restreint.

    - Pour les clients SMB qui accèdent à partager des données, dont ils ont besoin d’informations d’identification utilisateur associées au partage. Ces informations d’identification sont définies au moment de la création du partage.
    - Pour les clients NFS accéder aux partages, les adresses IP des clients doivent être ajoutés au moment de la création du partage.