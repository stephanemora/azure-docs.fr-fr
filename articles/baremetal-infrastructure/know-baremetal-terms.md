---
title: Connaître les termes d’Azure BareMetal Infrastructure
description: Connaître les termes d’Azure BareMetal Infrastructure.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725455"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Connaître les termes relatifs à BareMetal Infrastructure

Dans cet article, nous allons aborder des termes importants liés à BareMetal Infrastructure.

- **Révision** : Il existe deux révisions de tampons pour les tampons BareMetal Infrastructure (HANA - Grandes instances). Elles diffèrent des ordinateurs hôtes de l’ordinateur virtuel Azure par leur architecture et leur proximité :
    - « Révision 3 » (Rév. 3) : La conception originale déployée mi-2016.
    - « Révision 4.2 » (Rév. 4.2) : Une nouvelle conception qui offre une plus grande proximité avec les ordinateurs hôtes de l’ordinateur virtuel Azure, avec une latence réseau ultra-faible entre les machines virtuelles Azure et HANA - Grandes instances. Les ressources du portail Azure sont appelées « BareMetal Infrastructure », et les clients peuvent accéder à leurs ressources en tant qu’instances BareMetal à partir du portail Azure.

- **Tampon** : définit la taille de déploiement interne de Microsoft pour les instances BareMetal. Avant que des instances puissent être déployées, un tampon d’instance BareMetal constitué de racks de calcul, de réseau et de stockage doit être déployé dans un centre de données. Un tel déploiement est nommé tampon d’instance BareMetal.

- **Locataire** : un client déployant un tampon d’instance BareMetal est isolé en tant que *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles au niveau du tampon de l’instance BareMetal. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires au niveau du tampon d’instance BareMetal.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez présenté une terminologie importante de BareMetal Infrastructure, vous pouvez en savoir plus sur les éléments suivants :
- Plus d’informations sur [BareMetal Infrastructure](concepts-baremetal-infrastructure-overview.md).
- Comment [Connecter des instances BareMetal Infrastructure dans Azure](connect-baremetal-infrastructure.md).

