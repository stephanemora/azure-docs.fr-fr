---
title: Connaître les termes d’Azure BareMetal Infrastructure
description: Connaître les termes d’Azure BareMetal Infrastructure.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861915"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Connaître les termes relatifs à BareMetal Infrastructure

Dans cet article, nous allons aborder des termes importants relatifs à BareMetal.

- **Révision** : il existe une révision originale des tampons appelée Révision 3 (Rev 3) et deux révisions différentes des tampons Instance BareMetal. Chaque tampon diffère des hôtes de machines virtuelles Azure en matière d’architecture et de proximité :
    - **Révision 4** (Rev 4) : conception plus récente qui assure une plus grande proximité des hôtes de machine virtuelle Azure et réduit la latence entre les machines virtuelles Azure et les unités Instance BareMetal. 
    - **Révision 4.2** (Rev 4.2) :dernière infrastructure BareMetal rebaptisée qui utilise l’architecture Rev 4 existante. Rev 4 assure une plus grande proximité des hôtes de machine virtuelle Azure. Elle présente des améliorations significatives de latence réseau entre les machines virtuelles Azure et les unités d’instance BareMetal déployées dans les tampons ou lignes Rev 4. Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure.    

- **Tampon** : définit la taille de déploiement interne de Microsoft pour les instances BareMetal. Avant que des unités d’instance puissent être déployées, un tampon Instance BareMetal constitué de racks de calcul, de réseau et de stockage doit être déployé dans un centre de données. Un tel déploiement est nommé tampon Instance BareMetal ou à partir du nom de la Révision 4.2.

- **Tenant** : un client déployé dans le tampon Instance BareMetal est isolé dans un *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles au niveau du tampon Instance BareMetal. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires au niveau du tampon Instance BareMetal.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’[infrastructure BareMetal](workloads/sap/baremetal-overview-architecture.md) ou sur la façon d’[identifier et interagir avec des unités Instance BareMetal](workloads/sap/baremetal-infrastructure-portal.md). 