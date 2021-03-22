---
title: Tutoriel - Supprimer un cloud privé Azure VMware Solution
description: Découvrez comment supprimer un Cloud privé Azure VMware Solution dont vous n’avez plus besoin.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462097"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Supprimer un cloud privé Azure VMware Solution

Si vous avez un cloud privé Azure VMware Solution dont vous n’avez plus besoin, vous pouvez le supprimer. Le cloud privé comprend un domaine de réseau isolé, un ou plusieurs clusters vSphere provisionnés sur des hôtes serveur dédiés et plusieurs machines virtuelles. Quand vous supprimez un cloud privé, toutes les machines virtuelles, leurs données et les clusters sont supprimés. Les hôtes Azure VMware Solution dédiés sont réinitialisés sans problème et retournés au pool libre. L’espace d’adressage réseau provisionné est également supprimé.  

> [!CAUTION]
> La suppression du cloud privé est une opération irréversible. Une fois le cloud privé supprimé, les données ne peuvent pas être récupérées, car cela met fin à l’ensemble des charges de travail et composants en cours d’exécution, et détruit toutes les données et tous les paramètres de configuration du cloud privé, notamment les adresses IP publiques.

## <a name="prerequisites"></a>Prérequis

Si vous avez besoin des machines virtuelles et de leurs données ultérieurement, veillez à sauvegarder les données avant de supprimer le cloud privé.  Il n’existe aucun moyen de récupérer les machines virtuelles et leurs données.


## <a name="delete-the-private-cloud"></a>Supprimer le cloud privé

1. Accédez à la console Azure VMware Solutions dans le [portail Azure](https://portal.azure.com).

2. Sélectionnez le cloud privé à supprimer.
 
3. Entrez le nom du cloud privé, puis sélectionnez **Oui**. 

>[!NOTE]
>Le processus de suppression prend quelques heures.  
