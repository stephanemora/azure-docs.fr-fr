---
title: Tutoriel - Mettre à l’échelle un cloud privé
description: Dans ce tutoriel, vous utilisez le portail Azure pour mettre à l’échelle un cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 2129a3f5d04311883369b7b708689a13f07ec118
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463596"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Mettre à l’échelle un cloud privé Azure VMware Solution

Pour tirer le meilleur parti de votre expérience de cloud privé Azure VMware Solution, mettez à l’échelle les clusters et les hôtes selon vos besoins pour les charges de travail planifiées. Vous pouvez mettre à l’échelle les clusters et les hôtes d’un cloud privé en fonction des besoins de la charge de travail de vos applications. Les limitations de performances et de disponibilité pour des services spécifiques doivent être traitées au cas par cas. Les limites de clusters et d’hôtes sont indiquées dans l’[article sur le concept de cloud privé](concepts-private-clouds-clusters.md).

Dans ce tutoriel, nous allons utiliser le portail Azure pour :

> [!div class="checklist"]
> * Ajouter un cluster à un cloud privé existant
> * Ajoutez des hôtes à un cluster existant

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’un cloud privé existant. Si vous n’avez pas créé de cloud privé, utilisez le [tutoriel Créer un cloud privé](tutorial-create-private-cloud.md) pour en créer un. 

## <a name="add-a-new-cluster"></a>Ajouter un nouveau cluster

1. Dans la page Vue d’ensemble d’un cloud privé existant, sous **Gérer**, sélectionnez **Mettre à l’échelle le cloud privé**. Ensuite, sélectionnez **+ Ajouter un cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Sélectionner Ajouter un cluster" border="true":::

1. Dans la page **Ajouter un cluster**, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez **Enregistrer**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Dans la page Ajouter un cluster, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez Enregistrer." border="true":::

   Le déploiement du nouveau cluster va commencer.

## <a name="scale-a-cluster"></a>Mettre à l’échelle un cluster 

1. Dans la page Vue d’ensemble d’un cloud privé existant, sélectionnez **Mettre à l’échelle le cloud privé**, puis sélectionnez l’icône de crayon pour modifier le cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Sélectionner Mettre à l’échelle le cloud privé dans Vue d’ensemble" border="true":::

1. Dans la page **Modifier le cluster**, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez **Enregistrer**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Dans la page Modifier le cluster, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez Enregistrer." border="true":::

   L’ajout des hôtes au cluster commence.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’un autre cloud privé Azure VMware Solution, [créez un autre cloud privé](tutorial-create-private-cloud.md) en respectant les mêmes prérequis réseau et les mêmes limites de clusters et d’hôtes.

<!-- LINKS - external-->

<!-- LINKS - internal -->
