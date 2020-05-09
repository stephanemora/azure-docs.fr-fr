---
title: Niveau de sécurité dans Azure Security Center | Microsoft Docs
description: " Classez vos recommandations de sécurité par ordre de priorité à l’aide du degré de sécurisation dans Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415765"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Amélioration du degré de sécurisation dans Azure Security Center

> [!NOTE]
> Cet article concerne la version précédente du score sécurisé. Ce score sécurisé est toujours disponible à partir de l’interface utilisateur, mais il sera progressivement supprimé. Les deux expériences de score sécurisé s’exécutent côte à côte pour permettre une transition plus fluide.
>
> Pour plus d’informations sur le score de sécurité plus récent, voir [ici](secure-score-security-controls.md).
>

Avec autant de services offrant des avantages en matière de sécurité, il est souvent difficile de connaître les étapes à suivre en premier lieu pour sécuriser et renforcer votre charge de travail. L’outil Niveau de sécurité vérifie vos recommandations de sécurité et les classe par priorité pour que vous sachiez quelles recommandations appliquer en premier lieu. Cela vous permet de rechercher les vulnérabilités de sécurité les plus graves afin de définir les priorités d’investigation. L’outil Niveau de sécurité vous aide à évaluer l’état de sécurité de votre charge de travail.

## <a name="secure-score-calculation"></a>Calcul du niveau de sécurité

Azure Security Center imite le travail des analystes en sécurité, en examinant vos recommandations en matière de sécurité et en appliquant des algorithmes avancés pour déterminer le degré d’importance de chaque recommandation.
Azure Security Center vérifie constamment vos recommandations actives et calcule votre niveau de sécurité en fonction de celles-ci. Le niveau d’une recommandation est basé sur sa gravité et sur les bonnes pratiques de sécurité qui affectent le plus la sécurité de votre charge de travail.

Security Center vous fournit également un **niveau de sécurité général**. 

Le **niveau de sécurité général** est un cumul de tous vos niveaux de recommandation. Vous pouvez voir votre niveau de sécurité général dans vos abonnements ou vos groupes d’administration en fonction de votre sélection. Celui-ci varie en fonction de l’abonnement sélectionné et des recommandations actives correspondantes.

Pour identifier les recommandations qui ont le plus d’incidence sur votre niveau de sécurité, vous pouvez voir les trois recommandations ayant le plus d’impact dans le tableau de bord de Security Center. Vous pouvez également trier les recommandations dans le panneau de la liste de recommandations à l’aide de la colonne **Impact du niveau de sécurité**.

Pour voir votre niveau de sécurité général :

1. Dans le tableau de bord Azure, cliquez sur **Security Center**, puis sur **Niveau de sécurité**.

2. En haut, vous pouvez voir les points clés du niveau de sécurité :
   - Le **Niveau de sécurité général** représente le niveau par stratégie et par abonnement sélectionné
   - Le **Niveau de sécurité par catégorie** vous indique quelles sont les ressources qui nécessitent le plus d’attention
   - Le paramètre **Recommandations principales par impact sur le niveau de sécurité** vous propose une liste de recommandations qui améliorent au maximum votre niveau de sécurité, si vous les implémentez.
 
   ![Degré de sécurisation](./media/security-center-secure-score/secure-score-dashboard.png)

3. Dans le tableau ci-dessous, vous pouvez voir chacun de vos abonnements ainsi que le niveau de sécurité général correspondant.

   > [!NOTE]
   > La somme du niveau de sécurité de chaque abonnement n’est pas égale au niveau de sécurité général. Le niveau de sécurité est un calcul basé sur le rapport entre vos ressources saines et vos ressources totales par recommandation, et non une somme des niveaux de sécurité de tous vos abonnements. 
   >
4. Cliquez sur **Afficher les recommandations** pour voir les recommandations de l’abonnement correspondant. Vous pouvez les corriger pour améliorer votre niveau de sécurité.
4. Dans la liste des recommandations, vous pouvez voir que pour chacune d’elles, il existe une colonne qui représente l’**impact sur le niveau de sécurité**. Ce nombre indique l’amélioration de votre niveau de sécurité général, si vous suivez les recommandations. Par exemple, dans l’écran ci-dessous, si vous **corrigez les vulnérabilités dans les configurations de sécurité du conteneur**, votre niveau de sécurité augmente de 35 points.

   ![Degré de sécurisation](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Niveau de sécurité individuel

De plus, les niveaux de sécurité individuels que vous pouvez voir se trouvent dans le panneau des recommandations individuelles.  

Le **Score de la recommandation** est un calcul basé sur le rapport entre vos ressources saines et vos ressources totales. Si le nombre de ressources saines est égal au nombre total de ressources, vous obtenez le niveau de sécurité maximal de la recommandation, à savoir 50. Pour tenter d’obtenir le niveau de sécurité le plus proche du score maximal, corrigez les ressources non saines en suivant les recommandations.

L’**Impact de la recommandation** vous permet de connaître l’amélioration de votre niveau de sécurité, si vous appliquez les étapes de la recommandation. Par exemple, si votre niveau de sécurité est égal à 42 et si l’**Impact de la recommandation** est de +3, l’exécution des étapes décrites dans la recommandation vous permet de faire passer votre score à 45.

La recommandation présente les menaces auxquelles votre charge de travail est exposée si les mesures correctives ne sont pas prises.

![score de la recommandation individuelle](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment améliorer l’état de sécurité à l’aide du **niveau de sécurité** dans Azure Security Center. Pour plus d’informations sur Security Center, consultez :

* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
