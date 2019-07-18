---
title: Créer une solution Azure VMware par cloud privé CloudSimple
description: Décrit comment créer un cloud privé CloudSimple pour étendre les charges de travail VMware sur le cloud à l’aide avec la continuité des activités et la flexibilité opérationnelle
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332314"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Créer un cloud privé CloudSimple

La création d’un cloud privé vous permet de répondre à divers besoins courants pour l’infrastructure réseau :

* **Croissance**. Si vous avez atteint un point d’actualisation du matériel pour votre infrastructure existante, un cloud privé vous permet de développer sans aucun investissement matériel nouveau requis.

* **Expansion rapide**. En cas de besoins en capacité temporaire ou non planifiée, un cloud privé vous permet de créer de la capacité supplémentaire sans délai.

* **Protection accrue**. Avec un cloud privé de trois nœuds ou plus, vous bénéficiez de la redondance automatique et de la protection haute disponibilité.

* **Besoins d’infrastructure à long terme**. Si vos centres de données sont au maximum de leur capacité ou que vous souhaitez restructurer pour réduire vos coûts, un cloud privé vous permet de mettre hors service des centres de données et de migrer vers une solution cloud tout en maintenant la compatibilité avec les opérations de votre entreprise.

Lorsque vous créez un cloud privé, vous obtenez un cluster vSphere unique et toutes les machines virtuelles qui sont créées dans ce cluster de gestion.

## <a name="before-you-begin"></a>Avant de commencer

Les nœuds doivent être configurés avant de pouvoir créer votre cloud privé.  Pour plus d’informations sur l’approvisionnement des nœuds, consultez l’article [Approvisionner des nœuds pour la Solution VMware de CloudSimple - Azure](create-nodes.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accéder au portail CloudSimple

Accédez au [portail CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Créer un nouveau cloud privé

1. Sur la page **Ressources**, cliquez sur **Nouveau cloud privé**.

    ![Créer un cloud privé - Bien démarrer](media/create-pc-button.png)

2. Sélectionnez l’emplacement pour héberger les ressources de cloud privé.

3. Choisissez le type de nœud CS28 ou CS36 que vous avez configuré pour le cloud privé. Cette dernière option inclut la capacité de calcul et de mémoire maximale.

4. Sélectionnez le nombre de nœuds pour le cloud privé. Vous pouvez sélectionner au plus le nombre de nœuds disponibles que vous avez [configurés](create-nodes.md).

    ![Créer un cloud privé - Paramètress de base](media/create-private-cloud-basic-info.png)

5. Cliquez sur **Suivant : Options avancées**.

6. Entrez la plage CIDR pour les sous-réseaux vSphere/vSAN. Assurez-vous que la plage CIDR ne chevauche pas votre réseau local, d’autres sous-réseaux Azure (réseaux virtuels) ou le sous-réseau de la passerelle.  N’utilisez aucune plage CIDR définie sur les réseaux virtuels Azure.
    
    **Options de la plage CIDR :** /24, /23, /22 ou /21. Une plage CIDR /24 prend en charge jusqu'à neuf nœuds, une plage /23 jusqu'à 41 nœuds et une plage /22 ou /21 jusqu'à 64 nœuds (le nombre maximal de nœuds dans un cloud privé).

    > [!CAUTION]
    > Les adresses IP dans la plage CIDR de vSphere/vSAN est réservée pour une utilisation par l’infrastructure de cloud privé.  N’utilisez pas d’adresses IP de cette plage pour vos machines virtuelles.

7. Cliquez sur **Suivant : Examiner et créer**.

8. Passez en revue les paramètres. Si vous avez besoin modifier des paramètres, cliquez sur **Précédent**.

9. Cliquez sur **Créer**.

L’approvisionnement du cloud privé démarrera une fois que vous cliquerez sur Créer.  Vous pouvez surveiller la progression à partir de la page [Tâches](https://docs.azure.cloudsimple.com/activity/#tasks) du portail CloudSimple.  L’approvisionnement peut prendre de 30 minutes à deux heures.  Vous recevrez un e-mail une fois l’approvisionnement terminé.

## <a name="next-steps"></a>Étapes suivantes

* [Développer un cloud privé](expand-private-cloud.md)