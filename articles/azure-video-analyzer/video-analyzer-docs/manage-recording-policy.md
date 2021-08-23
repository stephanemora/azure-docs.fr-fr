---
title: Gérer la stratégie d’enregistrement avec Azure Video Analyzer
description: Cette rubrique explique comment gérer la stratégie d’enregistrement avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 31b9d4f14a9c60687b9a6e8f32fcc3a1fba82856
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601573"
---
# <a name="manage-recording-policy-with-video-analyzer"></a>Gérer la stratégie d’enregistrement avec Azure Video Analyzer

Vous pouvez utiliser Azure Video Analyzer pour [enregistrer](video-recording.md) des vidéos en direct dans le cloud pendant une période de plusieurs semaines, mois ou années. Cet enregistrement peut être [continu](continuous-video-recording.md) ou fragmenté, ou [basé sur les événements](event-based-video-recording-concept.md). Dans les deux cas, les enregistrements peuvent s’étendre sur plusieurs années. Vous pouvez gérer la durée (en jours) de cet archivage dans le cloud à l'aide des [outils de gestion du cycle de vie](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) intégrés au service Stockage Azure.  

Votre compte Video Analyzer est lié à un compte Stockage Microsoft Azure et, lorsque vous enregistrez dans une ressource vidéo, les données multimédia sont écrites dans un conteneur du compte de stockage. La gestion du cycle de vie vous permet de définir une [stratégie](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) applicable à un compte de stockage, dans laquelle vous pouvez spécifier une [règle](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) telle que la suivante.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

La règle ci-dessus :

* s'applique à tous les objets blob de blocs du compte Stockage ;
* spécifie qu'au bout de 30 jours, les objets blob sont déplacés du [niveau d'accès chaud au niveau d'accès froid](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* En outre, lorsque les objets blob ont plus de 90 jours, ils doivent être supprimés.

Lorsque vous utilisez Azure Video Analyzer pour enregistrer sur une ressource vidéo, vous spécifiez une propriété `segmentLength` qui spécifie la durée minimale de la vidéo (en secondes) à agréger avant qu’elle ne soit écrite dans le compte de stockage. Votre ressource vidéo contient une série de segments, chacun avec un horodatage de création `segmentLength` plus récent que le précédent. Lorsque la stratégie de gestion du cycle de vie démarre, elle supprime les segments antérieurs au seuil spécifié. Toutefois, vous pourrez continuer à accéder aux segments restants via les API Video Analyzer et à les lire. Pour plus d’informations, consultez [Lecture des enregistrements](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limites

Voici quelques limites connues de la gestion du cycle de vie :

* La stratégie peut contenir un maximum de 100 règles, et chaque règle peut spécifier 10 conteneurs. Ainsi, si vous avez besoin de différentes stratégies d'enregistrement (par exemple, 3 jours d'archivage pour la caméra située face au parking, 30 jours pour la caméra située sur le quai de chargement, et 180 jours pour la caméra située derrière la caisse), un seul compte de stockage vous permet de personnaliser les règles de 1 000 caméras.
* Les mises à jour de la stratégie de gestion du cycle de vie ne sont pas immédiates. Pour plus d'informations, consultez [cette section FAQ](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq).
* Si vous choisissez d'appliquer une stratégie qui déplace les objets blob vers le niveau froid, la lecture de cette partie de l'archive peut être affectée. Vous pouvez constater des latences supplémentaires, ou des erreurs sporadiques. Video Analyzer ne prend pas en charge la lecture du contenu au niveau archive.

## <a name="next-steps"></a>Étapes suivantes

[Lecture des enregistrements](playback-recordings-how-to.md)
