---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177518"
---
Les mises en garde suivantes s’appliquent pour les déplacements des données dans Azure.

- Nous vous suggérons de ne pas autoriser plusieurs appareils à écrire dans le même conteneur.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, l’appareil remplace le fichier dans le cloud.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers de partage n’est pas chargée dans les conteneurs d’objets blob.
- Vous pouvez copier les données à l’aide de la fonction glisser-déposer de l’Explorateur de fichiers, ou via la ligne de commande. Si la taille globale des fichiers copiés est supérieure à 10 Go, nous vous recommandons d’utiliser un programme de copie en bloc tel que Robocopy ou rsync. Les outils de copie en bloc tentent à nouveau l’opération de copie en cas d’erreurs intermittentes et offrent une résilience supplémentaire.
- Si le partage associé au conteneur de stockage Azure télécharge des objets BLOB qui ne correspondent pas au type d’objets BLOB définis pour le partage au moment de la création, alors de tels objets BLOB ne sont pas mis à jour. Par exemple, vous pouvez créer un partage d’objets blob de blocs sur l’appareil. Associez le partage avec un conteneur cloud existant présentant des objets blob de pages. Actualisez ce partage pour télécharger les fichiers. Modifiez quelques-uns des fichiers actualisés qui sont déjà stockés en tant qu’objets blob de pages dans le cloud. Vous verrez des échecs de chargement.
