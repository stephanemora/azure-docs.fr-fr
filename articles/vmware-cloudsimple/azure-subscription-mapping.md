---
title: Créer des liste de ressources partagées avec le mappage de l’abonnement Azure
description: Décrit comment créer des listes de ressources partagées pour votre cloud privé AVS grâce au mappage de l’abonnement Azure
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014961"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Créer des listes de ressources partagées pour votre cloud privé AVS grâce au mappage de l’abonnement Azure
Le mappage de l’abonnement Azure vous permet de créer des liste de ressources partagées pour votre cloud privé AVS à partir des liste de ressources partagées vSphere disponibles. Dans le portail AVS, vous pouvez afficher et gérer l’abonnement Azure pour vos clouds privés AVS.

> [!NOTE]
> Le mappage d’une liste de ressources partagées mappe également toutes les liste de ressources partagées enfants. Un pool de ressources parent ne peut pas être mappé si l’un de ses pools de ressources enfants est déjà mappé.

1. [Accédez au portail AVS](access-cloudsimple-portal.md).
2. Ouvrez la page **Ressources** et sélectionnez **Mappage d’abonnements Azure**.  
3. Cliquez sur **Edit Azure subscription mapping** (Modifier le mappage des abonnements Azure).  
4. Pour mapper les pools de ressources disponibles, sélectionnez-les sur la gauche et cliquez sur la flèche pointant à droite. 
5. Pour supprimer des mappages, sélectionnez-les sur la droite et cliquez sur la flèche pointant à gauche. 

    ![Abonnements Azure](media/resources-azure-mapping.png)

6. Cliquez sur **OK**.
