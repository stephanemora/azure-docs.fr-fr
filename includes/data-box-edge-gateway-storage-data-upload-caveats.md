---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966651"
---
Les mises en garde suivantes s’appliquent pour les déplacements des données dans Azure.

- Nous vous suggérons de ne pas autoriser plusieurs appareils à écrire dans le même conteneur.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, l’appareil remplace le fichier dans le cloud.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers de partage n’est pas chargée dans les conteneurs d’objets blob.
- Pour les fichiers volumineux, nous vous conseillons d’utiliser robocopy qui retente l'opération de copie en cas d'erreurs intermittentes.
