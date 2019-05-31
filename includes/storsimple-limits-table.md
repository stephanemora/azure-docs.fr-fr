---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238192"
---
| Identificateur de la limite | Limite | Commentaires |
| --- | --- | --- |
| Nombre maximal d'informations d'identification du compte de stockage |64 | |
| Nombre maximal de conteneurs de volumes |64 | |
| Nombre maximal de volumes |255 | |
| Nombre maximal de planifications par modèle de bande passante |168 |Une planification pour toutes les heures, tous les jours de la semaine. |
| Taille maximale d'un volume à plusieurs niveaux sur les appareils physiques |64 To pour StorSimple 8100 et StorSimple 8600 |StorSimple 8100 et StorSimple 8600 sont des périphériques physiques. |
| Taille maximale d'un volume à plusieurs niveaux sur les appareils virtuels dans Azure |30 To pour StorSimple 8010 <br></br> 64 To pour StorSimple 8020 |StorSimple 8010 et 8020 de StorSimple sont des appareils virtuels dans Azure qui utilisent le stockage Standard et Premium storage, respectivement. |
| Taille maximale d'un volume épinglé localement sur les appareils physiques |9 To pour StorSimple 8100 <br></br> 24 To pour StorSimple 8600 |StorSimple 8100 et StorSimple 8600 sont des périphériques physiques. |
| Nombre maximal de connexions iSCSI |512 | |
| Nombre maximal de connexions iSCSI à partir d'initiateurs |512 | |
| Nombre maximal d'enregistrements de contrôle d'accès par appareil |64 | |
| Nombre maximal de volumes par stratégie de sauvegarde |24 | |
| Nombre maximal de sauvegardes conservées par stratégie de sauvegarde |64 | |
| Nombre maximal de planifications par stratégie de sauvegarde |10 | |
| Nombre maximal d'instantanés de n'importe quel type pouvant être conservés par volume |256 |Cette quantité inclut les instantanés locaux et les instantanés cloud. |
| Nombre maximal d'instantanés pouvant être présents dans n'importe quel appareil |10 000 | |
| Nombre maximal de volumes pouvant être traités en parallèle pour sauvegarder, restaurer ou cloner |16 |<ul><li>S’il existe plus de 16 volumes, ils sont traités séquentiellement comme emplacements se libèrent.</li><li>Nouvelles sauvegardes d’un cloné ou un volume hiérarchisé restauré ne peut pas se produire jusqu'à ce que l’opération est terminée. Pour un volume local, les sauvegardes sont autorisées une fois le volume en ligne.</li></ul> |
| Temps de récupération d'un clone et d'une restauration pour les volumes à plusieurs niveaux |< 2 minutes |<ul><li>Le volume sera disponible dans les 2 minutes d’une restauration ou une opération de clonage, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, comme la plupart des données et des métadonnées se trouvent toujours dans le cloud. Performances peuvent s’améliorer en tant que flux de données à partir du cloud à l’appareil StorSimple.</li><li>La durée totale de téléchargement des métadonnées dépend de la taille du volume alloué. Les métadonnées sont automatiquement intégrées à l’appareil en arrière-plan, à une vitesse de 5 minutes par To de données du volume alloué. Ce taux peut être affecté par la bande passante Internet vers le cloud.</li><li>La restauration ou le clonage est terminé lorsque toutes les métadonnées se trouvent sur l’appareil.</li><li>Opérations de sauvegarde ne peut pas être exécutées jusqu'à la restauration ou le clonage n’est pas entièrement terminé. |
| Temps de récupération d'une restauration pour les volumes épinglés localement |< 2 minutes |<ul><li>Le volume sera disponible dans les 2 minutes suivant la restauration, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, comme la plupart des données et des métadonnées se trouvent toujours dans le cloud. Performances peuvent s’améliorer en tant que flux de données à partir du cloud à l’appareil StorSimple.</li><li>La durée totale de téléchargement des métadonnées dépend de la taille du volume alloué. Les métadonnées sont automatiquement intégrées à l’appareil en arrière-plan, à une vitesse de 5 minutes par To de données du volume alloué. Ce taux peut être affecté par la bande passante Internet vers le cloud.</li><li>Contrairement aux volumes à plusieurs niveaux, s’il existe des volumes épinglés localement, les données du volume sont également téléchargées localement sur l’appareil. L’opération de restauration est terminée lorsque toutes les données du volume ont été transférées vers l’appareil.</li><li>Les opérations de restauration peuvent être longues et la durée totale de la restauration dépend de la taille du volume local configuré, votre bande passante Internet et les données existantes sur l’appareil. Les opérations de sauvegarde sur le volume épinglé localement sont autorisées pendant que l'opération de restauration est en cours. |
| Disponibilité de la restauration légère |Dernier basculement | |
| Débit maximal du client en lecture/écriture, si traité à partir du niveau SSD * |920/720 Mo/s avec une seule interface de réseau Ethernet 10 gigabits |Jusqu'à deux heures, MPIO et deux interfaces réseau. |
| Débit maximal du client en lecture/écriture, si traité à partir de la couche de disque dur * |120/250 Mo/s | |
| Débit maximal du client en lecture/écriture, si traité à partir de la couche cloud * |11/41 Mo/s |Le débit de lecture dépend de si les clients génèrent et conservent une profondeur de file d’attente d’E/S suffisante. |

&#42;Débit maximal par type d’e/s a été mesuré avec 100 pour cent scénarios de lecture et écriture de 100 pour cent. Le débit réel peut être inférieur et varie en fonction d’e/s mélanger et conditions réseau.

