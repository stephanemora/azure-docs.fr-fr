---
title: Renforcement du réseau adaptative dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer la sécurisation renforcée réseau adaptative dans Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706607"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Renforcement du réseau adaptative dans Azure Security Center
Découvrez comment configurer le renforcement de réseau adaptative dans Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Quel est le renforcement de réseau adaptative ?
Application [réseau (NSG) des groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview) pour filtrer le trafic vers et à partir de ressources, améliore votre posture de sécurité réseau. Toutefois, il peut toujours être certains cas dans lesquels le trafic réel qui transite via le groupe de sécurité réseau est un sous-ensemble de règles de groupe de sécurité réseau définies. Dans ce cas, améliorer la posture de sécurité est possible en renforçant les règles du NSG, selon les modèles de trafic réel.

Renforcement de réseau ADAPTATIF fournit des recommandations pour renforcer les règles de groupe de sécurité réseau. Il utilise un algorithme de machine learning qui facteurs dans le trafic réel, appelée configuration approuvée, sur les menaces et autres indicateurs de compromission, et fournit ensuite des recommandations pour autoriser uniquement le trafic provenant des tuples IP/port spécifiques.

Par exemple, supposons que la règle de groupe de sécurité réseau existante consiste à autoriser le trafic à partir de 140.20.30.10/24 sur le port 22. Recommandation du ADAPTATIF réseau renforcement, basée sur l’analyse, serait à limiter la plage et autoriser le trafic à partir de 140.23.30.10/29 – qui est une plage IP plus étroite, et refuser tout autre trafic à ce port.

![affichage de renforcement de la sécurité réseau](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Recommandations de renforcement du réseau ADAPTATIF sont pris en charge sur les ports suivants : 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Afficher les alertes de renforcement de réseau adaptatif et des règles

1. Dans Security Center, sélectionnez **mise en réseau** -> **renforcement de réseau ADAPTATIF**. Les machines virtuelles de réseau sont répertoriés sous trois onglets distincts :
   * **Ressources défectueuses**: Machines virtuelles qui ont actuellement des recommandations et des alertes qui ont été déclenchées en exécutant l’algorithme adaptatif renforcement de réseau. 
   * **Ressources saines**: Machines virtuelles sans recommandations et des alertes.
   * **Non analysé ressources**: Machines virtuelles qui l’algorithme adaptatif renforcement de réseau ne peut pas être exécuté à cause d’une des raisons suivantes :
      * **Les machines virtuelles sont des machines virtuelles classiques**:-uniquement machines virtuelles Azure Resource Manager sont pris en charge.
      * **Pas assez de données est disponible**: Pour générer des recommandations de renforcement de trafic précis, Security Center requiert au moins 30 jours de données de trafic.
      * **Machine virtuelle n’est pas protégé par la norme ASC**: Seules les machines virtuelles qui sont définies au niveau tarifaire Standard de Security Center sont éligibles pour cette fonctionnalité.

     ![ressources défectueuses](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. À partir de la **ressources défectueuses** , sélectionnez une machine virtuelle pour afficher ses alertes et les règles de renforcement recommandée à appliquer.

    ![renforcement des alertes](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Passer en revue et adaptatif réseau renforcement des règles recommandées

1. À partir de la **ressources défectueuses** , sélectionnez une machine virtuelle. Les alertes et les règles de renforcement recommandée sont répertoriés.
   ![renforcement des alertes](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Le **règles** onglet répertorie les règles de sécurisation renforcée des adaptatif du réseau vous recommande de vous ajouter. Le **alertes** onglet répertorie les alertes qui ont été générées en raison de trafic, à la ressource, ce qui n’est pas dans la plage IP autorisée dans les règles recommandées.

   ![règles de sécurisation renforcée](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Si vous souhaitez modifier certains des paramètres d’une règle, vous pouvez le modifier, comme expliqué dans [modifier une règle](#modify-rule).
   > [!NOTE]
   > Vous pouvez également [supprimer](#delete-rule) ou [ajouter](#add-rule) une règle.

3. Sélectionnez les règles que vous souhaitez appliquer sur le groupe de sécurité réseau, puis cliquez sur **appliquer**. 

### Modifier une règle  <a name ="modify-rule"> </a>

Voulez-vous modifier les paramètres d’une règle qui a été recommandé. Par exemple, vous souhaiterez modifier les plages d’IP recommandées.

Certaines instructions importantes pour la modification d’une règle de sécurisation renforcée des adaptatif du réseau :

* Vous pouvez modifier les paramètres de règles de « autoriser » uniquement. 
* Vous ne pouvez pas modifier les règles de devenir « deny » règles « Autoriser ». 

  > [!NOTE]
  > Création et modification des règles de « refus » s’effectuent directement sur le groupe de sécurité réseau pour plus d’informations, consultez [créer, modifier ou supprimer un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Un **refuser tout le trafic** règle est le seul type de règle « deny » qui serait répertoriée ici, et elle ne peut pas être modifiée. Vous pouvez, toutefois, peut le supprimer (consultez [supprimer une règle](#delete-rule)).
  > [!NOTE]
  > Un **refuser tout le trafic** règle est recommandée lorsque, par conséquent de l’exécution de l’algorithme, Security Center n’identifie pas le trafic qui doit être autorisé, selon la configuration de groupe de sécurité réseau existante. Par conséquent, la règle recommandée consiste à refuser tout le trafic vers le port spécifié. Le nom de ce type de règle est affiché en tant que « générée par le système ». Après l’application de cette règle, son nom réel dans le groupe de sécurité réseau sera une chaîne composée le protocole, direction du trafic, « Refus » et un nombre aléatoire.

*Pour modifier une règle de sécurisation renforcée des adaptatif du réseau :*

1. Pour modifier certains des paramètres d’une règle, dans le **règles** onglet, cliquez sur les trois points (...) à la fin de ligne de la règle, puis cliquez sur **modifier la règle**.

   ![modifier la règle](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Dans le **modifier une règle** fenêtre, mettre à jour les détails que vous souhaitez modifier, puis cliquez sur **enregistrer**.

   > [!NOTE]
   > Après avoir cliqué sur **enregistrer**, vous avez modifié avec succès de la règle. *Toutefois, vous n’avez appliqué il pour le groupe de sécurité réseau.* Pour l’appliquer, vous devez sélectionner la règle dans la liste, puis cliquez sur **appliquer** (comme expliqué dans l’étape suivante).

3. Pour appliquer la règle de mise à jour, dans la liste, sélectionnez la règle de mise à jour, puis cliquez sur **appliquer**.

### Ajouter une nouvelle règle <a name ="add-rule"> </a>


Vous pouvez ajouter une règle « Autoriser » qui a été déconseillée par Security Center.

> [!NOTE]
> Règles uniquement « autoriser » peuvent être ajoutées ici. Si vous souhaitez ajouter des règles de « refus », vous pouvez effectuer directement sur le groupe de sécurité réseau. Pour plus d’informations, consultez [créer, modifier ou supprimer un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Pour ajouter une règle de sécurisation renforcée des adaptatif du réseau :*

1. Cliquez sur **ajouter une règle** (situé dans le coin supérieur gauche).

   ![Ajouter une règle](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Dans le **modifier une règle** fenêtre, entrez les détails, cliquez sur **enregistrer**.

   > [!NOTE]
   > Après avoir cliqué sur **enregistrer**, vous avez ajouté la règle, et elle est répertoriée avec les autres règles recommandées. Toutefois, vous n'avez pas appliquée sur le groupe de sécurité réseau. Pour l’activer, vous devez sélectionner la règle dans la liste, puis cliquez sur **appliquer** (comme expliqué dans l’étape suivante).

3. Pour appliquer la nouvelle règle, dans la liste, sélectionnez la nouvelle règle, puis cliquez sur **appliquer**.



### Supprimer une règle <a name ="delete-rule"> </a>

Lorsque cela est nécessaire, vous pouvez supprimer une règle recommandée. Par exemple, vous pouvez déterminer qu’appliquant une règle suggérée susceptibles de bloquer le trafic légitime.

*Pour supprimer une règle de sécurisation renforcée des adaptatif du réseau :*

1. Dans le **règles** onglet, cliquez sur les trois points (...) à la fin de ligne de la règle, puis cliquez sur **supprimer la règle**.

   ![Supprimer la règle](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

