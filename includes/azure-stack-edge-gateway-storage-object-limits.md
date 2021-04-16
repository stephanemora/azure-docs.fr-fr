---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 10d6ef2c90390f08e38726363416493f937de8f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465375"
---
Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite de chargement                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | 4,75 To                                                 |
| Objet blob de pages         | 1 To <br> Chaque fichier chargé au format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |
| Azure Files         | 1 To <br> Chaque fichier chargé au format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |

> [!IMPORTANT]
> La création de fichiers (quel que soit le type de stockage) est autorisée jusqu’à 5 To. Toutefois, si vous créez un fichier dont la taille est supérieure à la limite de chargement définie dans le tableau précédent, le fichier n’est pas chargé. Vous devez supprimer manuellement le fichier pour récupérer l’espace.
