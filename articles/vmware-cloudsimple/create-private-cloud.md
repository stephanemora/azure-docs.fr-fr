---
title: Azure VMware Solutions (AVS) - Créer un cloud privé AVS
description: Décrit comment créer un cloud privé AVS pour étendre les charges de travail VMware sur le cloud avec la continuité des activités et la flexibilité opérationnelle.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024787"
---
# <a name="create-an-avs-private-cloud"></a>Créer un cloud privé AVS

Un cloud privé AVS est une pile VMware isolée qui prend en charge les hôtes ESXi, vCenter, vSAN et NSX. Les clouds privés AVS sont managés par le biais du portail AVS. Ils ont leur serveur vCenter situé dans son propre domaine de gestion. La pile s’exécute sur des nœuds dédiés et des nœuds matériels nus (bare metal) isolés.

La création d’un cloud privé AVS vous permet de répondre à divers besoins courants pour l’infrastructure réseau :

* **Croissance**. Si vous avez atteint un point d’actualisation du matériel pour votre infrastructure existante, un cloud privé AVS vous permet de développer sans aucun investissement matériel nouveau requis.

* **Expansion rapide**. En cas de besoins en capacité temporaire ou non planifiée, un cloud privé AVS vous permet de créer de la capacité supplémentaire sans délai.

* **Protection accrue**. Avec un cloud privé AVS de trois nœuds ou plus, vous bénéficiez de la redondance automatique et de la protection haute disponibilité.

* **Besoins d’infrastructure à long terme**. Si vos centres de données sont au maximum de leur capacité ou que vous souhaitez restructurer pour réduire vos coûts, un cloud privé AVS vous permet de mettre hors service des centres de données et de migrer vers une solution cloud tout en maintenant la compatibilité avec les opérations de votre entreprise.

Quand vous créez un cloud privé AVS, vous obtenez un cluster vSphere unique et toutes les machines virtuelles qui sont créées dans ce cluster de gestion.

## <a name="before-you-begin"></a>Avant de commencer

Vous devez provisionner les nœuds avant de créer votre cloud privé AVS. Pour plus d’informations sur le provisionnement des nœuds, consultez [Provisionner des nœuds pour Azure VMware Solutions (AVS)](create-nodes.md).

Allouez une plage CIDR pour les sous-réseaux vSphere/vSAN pour le cloud privé AVS. Un cloud privé AVS est créé sous la forme d’un environnement isolé de la pile VMware (avec hôtes ESXi, vCenter, vSAN et NSX) managé par un serveur vCenter. Les composants de gestion sont déployés sur le réseau sélectionné pour la plage CIDR de sous-réseaux vSphere/vSAN. La plage CIDR du réseau est divisée en plusieurs sous-réseaux pendant le déploiement. L’espace d’adressage du sous-réseau vSphere/vSAN doit être unique. Il ne doit chevaucher aucun réseau qui communique avec l’environnement AVS. Les réseaux qui communiquent avec AVS incluent les réseaux locaux et les réseaux virtuels Azure. Pour plus d’informations sur les sous-réseaux vSphere/vSAN, consultez Vue d’ensemble des réseaux locaux virtuels et des sous-réseaux.

* Préfixe de plage CIDR de sous-réseaux vSphere/vSAN minimum : /24
* Préfixe de plage CIDR de sous-réseaux vSphere/vSAN maximum : /21


## <a name="access-the-avs-portal"></a>Accéder au portail AVS

Accédez au [portail AVS](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Créer un cloud privé AVS

1. Sélectionnez **Tous les services**.
2. Recherchez **Services AVS**.
3. Sélectionnez le service AVS où vous voulez créer votre cloud privé AVS.
4. Dans **Vue d’ensemble**, cliquez sur **Créer un cloud privé AVS** afin d’ouvrir un nouvel onglet de navigateur pour le portail AVS. Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.

    ![Créer un cloud privé AVS à partir d’Azure](media/create-private-cloud-from-azure.png)

5. Dans le portail AVS, spécifiez un nom pour votre cloud privé AVS.
6. Sélectionnez l’**Emplacement** de votre cloud privé AVS.
7. Sélectionnez le **Type de nœud** conformément à ce que vous avez acheté sur Azure.
8. Spécifiez le **Nombre de nœuds**. Au moins trois nœuds sont nécessaires pour créer un cloud privé AVS.
5. Dans le portail CloudSimple, attribuez un nom à votre cloud privé.
6. Sélectionnez l’**Emplacement** de votre cloud privé.
7. Sélectionnez le **Type de nœud**, conformément à ce que vous avez approvisionné sur Azure.
8. Spécifiez le **Nombre de nœuds**.  Au moins trois nœuds sont nécessaires pour créer un cloud privé.

    ![Créer un cloud privé AVS - Informations de base](media/create-private-cloud-basic-info.png)

9. Cliquez sur **Suivant : Options avancées**.
10. Entrez la plage CIDR pour les sous-réseaux vSphere/vSAN. Assurez-vous que la plage CIDR ne chevauche pas votre réseau local, d’autres sous-réseaux Azure (réseaux virtuels) ou le sous-réseau de la passerelle.

    **Options de la plage CIDR :** /24, /23, /22 ou /21. Une plage CIDR /24 prend en charge jusqu’à neuf nœuds, une plage /23 jusqu’à 41 nœuds et une plage /22 ou /21 jusqu’à 64 nœuds (le nombre maximal de nœuds dans un cloud privé AVS).

    > [!IMPORTANT]
    > L’utilisation des adresses IP de la plage CIDR vSphere/vSAN est réservée à l’infrastructure du cloud privé AVS. N’utilisez pas d’adresses IP de cette plage pour les machines virtuelles.

11. Cliquez sur **Suivant : Examiner et créer**.
12. Passez en revue les paramètres. Si vous avez besoin modifier des paramètres, cliquez sur **Précédent**.
13. Cliquez sur **Créer**.

Le processus de provisionnement du cloud privé AVS démarre. Le provisionnement du cloud privé AVS peut prendre jusqu’à deux heures.

Pour plus d’instructions sur le développement d’un cloud privé AVS existant, consultez [Développer un cloud privé AVS](expand-private-cloud.md).
