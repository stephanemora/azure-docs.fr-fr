---
title: 'Tutoriel : Mettre à l’échelle un cloud privé'
description: Dans ce tutoriel, vous utilisez le portail Azure pour mettre à l’échelle un cloud privé Azure VMware Solution (AVS) Preview.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739724"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Tutoriel : Mettre à l’échelle un cloud privé Azure VMware Solution (AVS) Preview

Pour tirer le meilleur parti de votre expérience de cloud privé Azure VMware Solution (AVS) Preview, adaptez les clusters et les hôtes à vos besoins pour les charges de travail planifiées. Comme la préversion d’AVS ne prend pas en charge vos vCenter locaux, vous devez utiliser ce que vous avez déjà créé via le portail Azure.

Vous pouvez adapter le nombre de clusters et le nombre d’hôtes d’un cloud privé en fonction des besoins de la charge de travail de vos applications. Les limites de performances et de disponibilité pour des services spécifiques doivent être traitées au cas par cas dans votre environnement cloud AVS Preview. Les limites de clusters et d’hôtes dans un cloud privé sont indiquées dans l’[article sur le concept de cloud privé](concepts-private-clouds-clusters.md).

Dans ce tutoriel, vous utilisez le portail Azure pour :

> [!div class="checklist"]
> * Ajouter un cluster à un cloud privé existant
> * Ajoutez des hôtes à un cluster existant

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’un cloud privé. Si vous n’avez pas encore créé de cloud privé, utilisez le [tutoriel Créer un cloud privé](tutorial-create-private-cloud.md) pour créer un cloud privé et configurez le réseau pour votre cloud privé VMware dans Azure pour mettre en place le réseau virtuel nécessaire.

## <a name="add-a-new-cluster"></a>Ajouter un nouveau cluster

Dans la page Vue d’ensemble d’un cloud privé existant, sous **Gérer**, sélectionnez **Mettre à l’échelle le cloud privé**. Ensuite, sélectionnez **+ Ajouter un cluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Sélectionner Ajouter un cluster" border="true":::

Dans la page **Ajouter un cluster**, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez **Enregistrer**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Configurer un nouveau cluster de cloud privé" border="true":::

Le déploiement du nouveau cluster va commencer.

## <a name="scale-a-cluster"></a>Mettre à l’échelle un cluster 

Dans la page Vue d’ensemble d’un cloud privé existant, sélectionnez **Mettre à l’échelle le cloud privé**, puis sélectionnez l’icône de crayon pour modifier le cluster.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Sélectionner Mettre à l’échelle le cloud privé dans Vue d’ensemble" border="true":::

Dans la page **Modifier le cluster**, utilisez le curseur pour sélectionner le nombre d’hôtes. Sélectionnez **Enregistrer**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Configurer un nouveau cluster de cloud privé" border="true":::

L’ajout des hôtes au cluster va commencer.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’un autre cloud privé AVS, [créez un autre cloud privé](tutorial-create-private-cloud.md) en respectant les mêmes prérequis pour le réseau, et les limites de clusters et d’hôtes.

<!-- LINKS - external-->

<!-- LINKS - internal -->
