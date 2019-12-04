---
title: Renforcement du réseau adaptatif dans Azure Security Center | Microsoft Docs
description: Découvrez comment renforcer vos règles de groupes de sécurité réseau (NSG) et améliorer votre position de sécurité, en fonction de modèles de trafic réels.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: memildin
ms.openlocfilehash: fb1e381f9b956a0c6414a82505aced2cbdb2d680
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559279"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Renforcement du réseau adaptatif dans Azure Security Center
Découvrez comment configurer le renforcement du réseau adaptatif dans Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Qu’est-ce que le renforcement du réseau adaptatif ?
L’application de [groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) pour filtrer le trafic vers et depuis des ressources améliore votre posture de sécurité réseau. Il peut toutefois rester des cas dans lesquels le trafic réel qui transite via le groupe de sécurité réseau est un sous-ensemble des règles NSG définies. Dans ces cas, une amélioration supplémentaire de la posture de sécurité est possible en renforçant les règles NSG en fonction des modèles de trafic réel.

Le renforcement du réseau adaptatif fournit des suggestions visant à renforcer encore les règles NSG. Il utilise un algorithme de Machine Learning factorisé dans le trafic réel, appelé une configuration approuvée, l’intelligence des menaces et d’autres indicateurs de compromis, puis fournit des suggestions pour autoriser uniquement le trafic provenant de tuples IP/port spécifiques.

Par exemple, supposons que la règle NSG existante consiste à autoriser le trafic provenant de 140.20.30.10/24 sur le port 22. La suggestion de renforcement du réseau adaptatif, basée sur l’analyse, consisterait à limiter la plage et à autoriser le trafic provenant de 140.23.30.10/29 (qui est une plage IP plus étroite) et à refuser tout autre trafic sur ce port.

![vue du renforcement du réseau](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> Les suggestions de renforcement du réseau adaptatif sont prises en charge sur les ports suivants : 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Afficher les alertes et règles de renforcement du réseau adaptatif

1. Dans Security Center, sélectionnez **Mise en réseau** -> **Renforcement du réseau adaptatif**. Les machines virtuelles du réseau sont répertoriées sous trois onglets distincts :
   * **Ressources non saines** : Machines virtuelles ayant actuellement des suggestions et des alertes qui ont été déclenchées en exécutant l’algorithme de renforcement du réseau adaptatif. 
   * **Ressources saines** : Machines virtuelles sans alertes ni suggestions.
   * **Ressources non analysées** : Machines virtuelles dont l’algorithme de renforcement du réseau adaptatif ne peut pas être exécuté pour l’une des raisons suivantes :
      * **Les machines virtuelles sont des machines virtuelles Classic** : Seules les ressources Azure Resource Manager sont prises en charge.
      * **Les données disponibles sont insuffisantes** : Pour générer des suggestions de renforcement du trafic précises, Security Center requiert au moins 30 jours de données de trafic.
      * **La machine virtuelle n’est pas protégée par la norme ASC** : Seules les machines virtuelles qui sont définies au niveau tarifaire Standard de Security Center sont éligibles pour cette fonctionnalité.

     ![ressources non saines](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Dans l’onglet **Ressources non saines**, sélectionnez une machine virtuelle pour afficher ses alertes et les règles de renforcement recommandées à appliquer.

    ![alertes de renforcement](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Passer en revue et appliquer des règles recommandées de renforcement du réseau adaptatif

1. Dans l’onglet **Unhealthy resources** (Ressources non saines), sélectionnez une machine virtuelle. Les alertes et règles de renforcement recommandées sont répertoriées.

     ![règles de renforcement](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > L’onglet **Règles** répertorie les règles que le renforcement du réseau adaptatif vous recommande d’ajouter. L’onglet **Alertes** répertorie les alertes qui ont été générées en raison de trafic, qui traverse la ressource, et qui ne se trouve pas dans la plages d’adresses IP autorisée dans les règles recommandées.

2. Si vous souhaitez modifier certains paramètres d’une règle, vous pouvez le faire comme expliqué dans [Modifier une règle](#modify-rule).
   > [!NOTE]
   > Vous pouvez également [supprimer](#delete-rule) ou [ajouter](#add-rule) une règle.

3. Sélectionnez les règles que vous souhaitez appliquer sur le groupe de sécurité réseau, puis cliquez sur **Appliquer**.

      > [!NOTE]
      > Les règles appliquées sont ajoutées aux groupes de sécurité réseau qui protègent la machine virtuelle. (Une machine virtuelle peut être protégée par un groupe de sécurité réseau qui est associé à sa carte réseau, ou le sous-réseau dans lequel réside la machine virtuelle, ou les deux)

    ![appliquer des règles](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Modifier une règle <a name ="modify-rule"> </a>

Vous souhaiterez peut-être modifier les paramètres d’une règle qui a été recommandée. Par exemple, vous souhaiterez modifier les plages d’IP recommandées.

Voici des instructions importantes relatives à la modification d’une règle de renforcement du réseau adaptatif :

* Vous pouvez modifier les paramètres de règles « allow » uniquement. 
* Vous ne pouvez pas modifier des règles « allow » en règles « deny ». 

  > [!NOTE]
  > La création et la modification des règles de « refus » sont effectuées directement sur le groupe de sécurité réseau. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Une règle **Deny all traffic** (Refuser tout le trafic) est la seule règle de type « deny » qui serait répertoriée ici, et elle ne peut pas être modifiée. Vous pouvez toutefois la supprimer (consultez [Supprimer une règle](#delete-rule)).
  > [!NOTE]
  > Une règle **Deny all traffic** (Refuser tout le trafic) est recommandée lorsque, suite à l’exécution de l’algorithme, Security Center n’identifie pas de trafic qui doit être autorisé en fonction de la configuration de groupe de sécurité réseau existante. Par conséquent, la règle recommandée consiste à refuser tout le trafic vers le port spécifié. Le nom de ce type de règle apparaît comme « *généré par le système* ». Après l’application de cette règle, son nom réel dans le groupe de sécurité réseau sera une chaîne constituée du protocole, de la direction du trafic, de « DENY » et d’un nombre aléatoire.

*Pour modifier une règle de renforcement du réseau adaptatif :*

1. Pour modifier certains paramètres d’une règle, dans l’onglet **Règles**, cliquez sur les trois points (...) à la fin de la ligne de la règle, puis cliquez sur **Modifier**.

   ![modifier une règle](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Dans la fenêtre **Modifier une règle**, mettez à jour les détails que vous souhaitez modifier, puis cliquez sur **Enregistrer**.

   > [!NOTE]
   > Après avoir cliqué sur **Enregistrer**, vous avez modifié avec succès de la règle. *Toutefois, vous ne l’avez pas appliquée au groupe de sécurité réseau.* Pour l’appliquer, vous devez sélectionner la règle dans la liste, puis cliquez sur **Appliquer** (comme expliqué dans l’étape suivante).

   ![modifier une règle](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Pour appliquer la règle mise à jour, sélectionnez la règle mise à jour dans la liste, puis cliquez sur **Appliquer**.

    ![appliquer une règle](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Ajouter une nouvelle règle <a name ="add-rule"> </a>

Vous pouvez ajouter une règle « allow » qui n’a pas été recommandée par Security Center.

> [!NOTE]
> Seules des règles « allow » peuvent être ajoutées ici. Si vous souhaitez ajouter des règles « deny », vous pouvez le faire directement sur le groupe de sécurité réseau. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Pour ajouter une règle de renforcement du réseau adaptatif :*

1. Cliquez sur **Ajouter une règle** (situé dans le coin supérieur gauche).

   ![ajouter une règle](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Dans la fenêtre **Nouvelle règle**, entrez les détails et cliquez sur **Ajouter**.

   > [!NOTE]
   > Après avoir cliqué sur **Ajouter**, vous avez ajouté la règle avec succès, et elle est répertoriée avec les autres règles recommandées. Toutefois, vous ne l’avez pas appliquée au groupe de sécurité réseau. Pour l’activer, vous devez sélectionner la règle dans la liste, puis cliquez sur **Appliquer** (comme expliqué dans l’étape suivante).

3. Pour appliquer la nouvelle règle, sélectionnez la nouvelle règle dans la liste, puis cliquez sur **Appliquer**.

    ![appliquer une règle](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Supprimer une règle <a name ="delete-rule"> </a>

Si nécessaire, vous pouvez supprimer une règle recommandée pour la session active. Par exemple, vous pouvez déterminer que l’application d’une règle suggérée est susceptible de bloquer du trafic légitime.

*Pour supprimer une règle de renforcement du réseau adaptatif pour votre session active :*

1. Dans l’onglet **Règles**, cliquez sur les trois points (...) à la fin de la ligne de la règle, puis cliquez sur **Supprimer**.  

    ![règles de renforcement](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

