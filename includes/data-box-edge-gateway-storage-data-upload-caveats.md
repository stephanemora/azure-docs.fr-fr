---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333898"
---
Les mises en garde suivantes s’appliquent pour les déplacements des données dans Azure.

- Nous vous suggérons de ne pas autoriser plusieurs appareils à écrire dans le même conteneur.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, l’appareil remplace le fichier dans le cloud.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers de partage n’est pas chargée dans les conteneurs d’objets blob.
- Pour les fichiers volumineux, nous vous conseillons d’utiliser robocopy qui retente l'opération de copie en cas d'erreurs intermittentes.
- Si le partage associé au conteneur de stockage Azure télécharge des objets BLOB qui ne correspondent pas au type d’objets BLOB définis pour le partage au moment de la création, alors de tels objets BLOB ne sont pas mis à jour. Par exemple, vous pouvez créer un partage d’objets blob de blocs sur l’appareil. Associez le partage avec un conteneur cloud existant présentant des objets blob de pages. Actualisez ce partage pour télécharger les fichiers. Modifiez quelques-uns des fichiers actualisés qui sont déjà stockés en tant qu’objets blob de pages dans le cloud. Vous verrez des échecs de chargement.
