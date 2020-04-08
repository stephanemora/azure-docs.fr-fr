---
title: Correctif rapide pour les recommandations Advisor
description: Effectuer une correction en bloc à l’aide d’un correctif rapide dans Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503925"
---
# <a name="quick-fix-remediation-for-advisor"></a>Application d’un correctif rapide pour Advisor
Un **correctif rapide** offre un moyen plus rapide et plus facile de corriger les recommandations dans plusieurs ressources. Il permet d’apporter des corrections en bloc aux ressources et d’optimiser vos abonnements plus rapidement grâce à une correction à grande échelle.
Cette fonctionnalité est disponible uniquement pour certaines recommandations dans le portail Azure.


## <a name="steps-to-use-quick-fix"></a>Utilisation d’un correctif rapide

1. Dans la liste des recommandations portant l’étiquette **Correctif rapide**, cliquez sur la recommandation.

   ![Correctif rapide Advisor](./media/quick-fix-1.png)
   
   *Les prix indiqués dans l’image sont fournis uniquement à titre d’exemple*

2. Dans la page Détails de la recommandation, vous verrez la liste des ressources pour lesquelles vous avez cette recommandation. Sélectionnez toutes les ressources que vous souhaitez corriger.

   ![Correctif rapide Advisor](./media/quick-fix-2.png)
   
   *Les prix indiqués dans l’image sont fournis uniquement à titre d’exemple*

3. Une fois que vous avez sélectionné les ressources, cliquez sur le bouton **Correctif rapide** pour effectuer une correction en bloc.

   > [!NOTE]
   > Certaines des ressources listées peuvent être désactivées, ce qui est le cas si vous n’avez pas les autorisations appropriées pour les modifier.
   
   > [!NOTE]
   > S’il y a d’autres implications, en plus des avantages mentionnés dans Advisor, vous en serez informé afin de prendre des décisions éclairées concernant les corrections.
   
4. Vous recevrez une notification une fois la correction terminée. Vous verrez une erreur s’afficher si des ressources ne sont pas corrigées. Ces ressources s’afficheront dans la liste des ressources dans mode sélectionné.  


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’excellence opérationnelle Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)
