---
title: Tutoriel - Supprimer un cloud privé Azure VMware Solution
description: Découvrez comment supprimer un Cloud privé Azure VMware Solution dont vous n’avez plus besoin.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ed1a4fdc2487bf470c96afa5ff8b3cde67336a40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730217"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Supprimer un cloud privé Azure VMware Solution

Si vous avez un cloud privé Azure VMware Solution dont vous n’avez plus besoin, vous pouvez le supprimer. Le cloud privé comprend les éléments suivants :

* un domaine réseau isolé

* un ou plusieurs clusters vSphere approvisionnés sur des hôtes de serveur dédié

* Plusieurs machines virtuelles

Lorsque vous supprimez un cloud privé, l’ensemble des machines virtuelles, des données, des clusters et de l’espace d’adressage réseau configurés sont supprimés. Les hôtes Azure VMware Solution dédiés sont réinitialisés sans problème et retournés au pool libre.   

> [!CAUTION]
> La suppression du cloud privé met fin à tous les composants et charges de travail en cours d’exécution et est une opération irréversible. Une fois que vous avez supprimé le cloud privé, vous ne pouvez pas récupérer les données.

## <a name="prerequisites"></a>Prérequis

Si vous avez besoin des machines virtuelles et de leurs données ultérieurement, veillez à sauvegarder les données avant de supprimer le cloud privé.  Malheureusement, il n’existe aucun moyen de récupérer les machines virtuelles et leurs données.


## <a name="delete-the-private-cloud"></a>Supprimer le cloud privé

1. Accédez à la console Azure VMware Solutions dans le [portail Azure](https://portal.azure.com).

2. Sélectionnez le cloud privé que vous souhaitez supprimer.
 
3. Entrez le nom du cloud privé, puis sélectionnez **Oui**. 

>[!NOTE]
>Le processus de suppression prend quelques heures.  
