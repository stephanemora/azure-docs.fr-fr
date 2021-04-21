---
title: Connaître les termes d’Azure BareMetal Infrastructure
description: Connaître les termes d’Azure BareMetal Infrastructure.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580129"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Connaître les termes relatifs à BareMetal Infrastructure

Dans cet article, nous allons aborder des termes importants liés à BareMetal Infrastructure.

- **Révision** : il existe une révision originale des tampons appelée Révision 3 (Rev 3) et deux révisions supplémentaires des tampons de l’instance BareMetal. Chaque tampon diffère des hôtes de machines virtuelles Azure en matière d’architecture et de proximité :
    - **Révision 4** (Rev 4) : conception plus récente qui assure une plus grande proximité des hôtes de machine virtuelle Azure et réduit la latence entre les machines virtuelles Azure et les instances SAP HANA. 
    - **Révision 4.2** (Rev 4.2) :dernière BareMetal Infrastructure rebaptisée qui utilise l’architecture Rev 4 existante. Rev 4 assure une plus grande proximité des hôtes de machine virtuelle Azure. Elle présente des améliorations significatives de latence réseau entre les machines virtuelles Azure et les instances BareMetal déployées dans les tampons ou lignes Rev 4. Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure.    

- **Tampon** : définit la taille de déploiement interne de Microsoft pour les instances BareMetal. Avant que des instances puissent être déployées, un tampon d’instance BareMetal constitué de racks de calcul, de réseau et de stockage doit être déployé dans un centre de données. Un tel déploiement est nommé tampon d’instance BareMetal.

- **Locataire** : un client déployant un tampon d’instance BareMetal est isolé en tant que *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles au niveau du tampon de l’instance BareMetal. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires au niveau du tampon d’instance BareMetal.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez présenté une terminologie importante de BareMetal Infrastructure, vous pouvez en savoir plus sur les éléments suivants :
- Plus d’informations sur [BareMetal Infrastructure](concepts-baremetal-infrastructure-overview.md).
- Comment [Connecter des instances BareMetal Infrastructure dans Azure](connect-baremetal-infrastructure.md).

