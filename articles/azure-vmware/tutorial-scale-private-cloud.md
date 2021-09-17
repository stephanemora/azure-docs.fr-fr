---
title: Tutoriel - Mettre à l’échelle des clusters dans un cloud privé
description: Dans ce tutoriel, vous utilisez le portail Azure pour mettre à l’échelle un cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.date: 08/03/2021
ms.openlocfilehash: 47bc7d12a025104df48d5a55af4abf342759696e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638790"
---
# <a name="tutorial-scale-clusters-in-a-private-cloud"></a>Tutoriel : Mettre à l’échelle des clusters dans un cloud privé

Pour tirer le meilleur parti de votre expérience de cloud privé Azure VMware Solution, mettez à l’échelle les clusters et les hôtes selon vos besoins pour les charges de travail planifiées. Vous pouvez mettre à l’échelle les clusters et les hôtes d’un cloud privé en fonction des besoins de la charge de travail de vos applications.  Traitez les limitations de performances et de disponibilité pour des services spécifiques doivent au cas par cas. 

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

Dans ce tutoriel, nous allons utiliser le portail Azure pour :

> [!div class="checklist"]
> * Ajouter un cluster à un cloud privé existant
> * Ajoutez des hôtes à un cluster existant

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’un cloud privé existant. Si vous n’avez pas créé de cloud privé, suivez le [tutoriel Créer un cloud privé](tutorial-create-private-cloud.md) pour en créer un. 

## <a name="add-a-new-cluster"></a>Ajouter un nouveau cluster

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Clusters** > **Ajouter un cluster**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Capture d’écran montrant comment ajouter un cluster à un cloud privé Azure VMware Solution." border="true":::

1. Utilisez le curseur pour sélectionner le nombre d’hôtes, puis sélectionnez **Enregistrer**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Capture d’écran montrant comment configurer un nouveau cluster." border="true":::

   Le déploiement du nouveau cluster commence.

## <a name="scale-a-cluster"></a>Mettre à l’échelle un cluster 

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Clusters**.

1. Sélectionnez le cluster que vous souhaitez mettre à l’échelle, **Plus** (...), puis **Modifier**.

   :::image type="content" source="media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Capture d’écran montrant où modifier un cluster existant." border="true":::

1. Utilisez le curseur pour sélectionner le nombre d’hôtes, puis sélectionnez **Enregistrer**.

   L’ajout des hôtes au cluster commence.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’un autre cloud privé Azure VMware Solution, [créez un autre cloud privé](tutorial-create-private-cloud.md) en respectant les mêmes prérequis réseau et les mêmes limites de clusters et d’hôtes.

<!-- LINKS - external-->

<!-- LINKS - internal -->
