---
title: Connaître les termes d’Azure BareMetal Infrastructure
description: Connaître les termes d’Azure BareMetal Infrastructure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829030"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Connaître les termes relatifs à BareMetal Infrastructure

Dans cet article, nous allons aborder des termes importants relatifs à BareMetal.

- **Révision** : il existe deux révisions de tampons pour les tampons Instance BareMetal. Celles-ci diffèrent des hôtes de machines virtuelles Azure en termes d’architecture et de proximité :
    - **Révision 3** (Rev 3) : est la conception d’origine.
    - **Révision 4** (Rev 4) : est une nouvelle conception qui assure une plus grande proximité des hôtes de machine virtuelle Azure et réduit la latence entre les machines virtuelles Azure et les unités d’instance BareMetal. 
    - **Révision 4.2** (Rev 4.2) :est la dernière infrastructure BareMetal rebaptisée qui utilise l’architecture Rev 4 existante. Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure.  

- **Tampon** : définit la taille de déploiement interne de Microsoft pour les instances BareMetal. Avant que des unités d’instance puissent être déployées, un tampon Instance BareMetal constitué de racks de calcul, de réseau et de stockage doit être déployé dans un emplacement de centre de données. Un tel déploiement est nommé tampon Instance BareMetal ou à partir du nom de la Révision 4.2.

- **Tenant** : un client déployé dans le tampon Instance BareMetal est isolé dans un *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles au niveau du tampon Instance BareMetal. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires au niveau du tampon Instance BareMetal.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment identifier et interagir avec les unités d’instance BareMetal par le biais du [portail Azure](workloads/sap/baremetal-infrastructure-portal.md).


 