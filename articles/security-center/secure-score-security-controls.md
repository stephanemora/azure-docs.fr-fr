---
title: Degré de sécurisation dans Azure Security Center
description: Description du degré de sécurisation d’Azure Security Center et de ses contrôles de sécurité
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: b19a7c156abf32e2a0f6d70717145a6ed5ab42ce
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099673"
---
# <a name="secure-score-in-azure-security-center"></a>Degré de sécurisation dans Azure Security Center

## <a name="introduction-to-secure-score"></a>Présentation du degré de sécurisation

Les deux principaux objectifs d'Azure Security Center sont les suivants : 

- Vous aider à comprendre votre situation actuelle en matière de sécurité
- Vous aider à améliorer efficacement votre sécurité

Pour vous permettre d'atteindre ces objectifs, Security Center met à votre disposition une fonctionnalité appelée **niveau de sécurité**.

Security Center évalue continuellement vos ressources, vos abonnements et votre organisation en recherchant d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible.

Sur les pages du portail Azure, le niveau de sécurité est indiqué sous forme de pourcentage, mais les valeurs sous-jacentes sont également clairement présentées :

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Score de sécurité global comme indiqué dans le portail":::

Pour renforcer votre sécurité, consultez la page des recommandations de Security Center. Chaque recommandation comprend des instructions pour vous aider à résoudre un problème spécifique.

Les recommandations sont regroupées en **contrôles de sécurité**. Chaque contrôle est un groupe logique de recommandations de sécurité associées, et reflète les surfaces d'attaque vulnérables. Votre degré de sécurisation n’augmente que si vous avez suivi *toutes* les recommandations fournies pour une même ressource au sein d’un contrôle. Pour connaître le degré de sécurisation de chacune des surfaces d'attaque de votre organisation, examinez le niveau de sécurité de chaque contrôle de sécurité.

Pour plus d’informations, consultez [Mode de calcul de votre degré de sécurisation](secure-score-security-controls.md#how-your-secure-score-is-calculated), ci-dessous. 

## <a name="how-your-secure-score-is-calculated"></a>Mode de calcul de votre degré de sécurisation 

La contribution de chaque contrôle de sécurité au degré de sécurisation global est indiquée clairement dans la page des recommandations.

[![La version améliorée du degré de sécurisation comprend désormais des contrôles de sécurité](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Pour obtenir le nombre maximal de points que peut avoir un contrôle de sécurité, toutes vos ressources doivent se conformer à l’intégralité des recommandations de sécurité fournies dans le contrôle de sécurité. Par exemple, Security Center fournit plusieurs recommandations relatives à la sécurisation de vos ports de gestion. Vous devrez toutes les appliquer pour voir votre degré de sécurisation s’améliorer.

Par exemple, le contrôle de sécurité « Appliquer les mises à jour système » a un degré maximal de six points, que vous pouvez voir dans l’info-bulle concernant l’augmentation potentielle de la valeur du contrôle :

[![Contrôle de sécurité « Appliquer les mises à jour système »](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Le score maximal pour ce contrôle, Appliquer des mises à jour système, est toujours 6. Dans cet exemple, il y a 50 ressources. Nous avons donc divisé le score maximal par 50 et le résultat est que chaque ressource contribue à 0,12 points. 

* **Augmentation potentielle** (0,12 x 8 ressources non saines = 0,96) - Les points disponibles que vous conservez dans le cadre du contrôle. Si vous corrigez toutes les suggestions de ce contrôle, votre score augmentera de 2 % (dans ce cas, 0,96 points arrondis à 1 point). 
* **Score courant** (0,12 x 42 ressources saines = 5,04) - Le score courant pour ce contrôle. Chaque contrôle contribue au score total. Dans cet exemple, le contrôle contribue à 5,04 points au total sécurisé courant.
* **Score maximal** - Nombre maximal de points que vous pouvez gagner en appliquant toutes les recommandations d’un contrôle. Le score maximal d’un contrôle indique l’importance relative de ce contrôle. Utilisez les valeurs du score maximal pour que le triage des problèmes fonctionne en premier. 


### <a name="calculations---understanding-your-score"></a>Calculs : comprendre votre score

|Métrique|Formule et exemple|
|-|-|
|**Degré de sécurisation actuel du contrôle de sécurité**|<br>![Équation pour le calcul du degré de sécurisation d’un contrôle de sécurité](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Chaque contrôle de sécurité individuel contribue au degré de sécurisation global. Chaque ressource concernée par une recommandation dans le contrôle contribue au degré de sécurisation actuel du contrôle. Le degré de sécurisation actuel de chaque contrôle correspond à la mesure de l’état des ressources *comprises* dans ce contrôle.<br>![Info-bulles présentant les valeurs utilisées lors du calcul du degré de sécurisation actuel du contrôle de sécurité](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Dans cet exemple, le score (ou degré) maximal de 6 est divisé par 78, car il s’agit de la somme des ressources saines et non saines.<br>6 / 78 = 0,0769<br>En multipliant ce score par le nombre de ressources saines (4), vous obtenez le score actuel :<br>0,0769 \* 4 = **0,31**<br><br>|
|**Degré de sécurisation**<br>Abonnement unique|<br>![Équation pour le calcul du score sécurisé d’un abonnement](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Degré de sécurisation d’un abonnement avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Dans cet exemple, vous pouvez voir un abonnement pour lequel tous les contrôles de sécurité sont disponibles (avec un degré de sécurisation maximal potentiel de 60 points). Le degré de sécurisation indique 28 points sur 60 points possibles, et les 32 points restants correspondent aux valeurs figurant dans la colonne « Potential score increase » (Augmentation potentielle du degré de sécurisation) des contrôles de sécurité.<br>![Liste des contrôles et augmentation potentielle du score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Degré de sécurisation**<br>Abonnements multiples|<br>![Équation pour le calcul du score sécurisé pour plusieurs abonnements](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Lors du calcul du score combiné pour plusieurs abonnements, Security Center comprend un *poids* pour chaque abonnement. Les pondérations relatives de vos abonnements sont déterminées par Security Center en fonction de facteurs tels que le nombre de ressources.<br>Le score actuel de chaque abonnement est calculé de la même façon que pour un abonnement unique, mais le poids est appliqué comme indiqué dans l’équation.<br>Quand vous consultez plusieurs abonnements, le degré de sécurisation évalue toutes les ressources de toutes les stratégies activées, et regroupe leur impact combiné sur le degré maximal de chaque contrôle de sécurité.<br>![Degré de sécurisation pour plusieurs abonnements avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Le score combiné **n’est pas** une moyenne. Il s’agit plutôt d’une évaluation de l’état de toutes les ressources de tous les abonnements.<br>Ici aussi, si vous accédez à la page des recommandations et si vous ajoutez les points que vous pouvez potentiellement gagner, vous constaterez qu’il s’agit de la différence entre le score actuel (24) et le score maximal possible (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quelles sont les recommandations incluses dans les calculs du degré de sécurisation ?

Seules les recommandations intégrées ont un impact sur le degré de sécurisation.

Les recommandations marquées **Preview (Préversion)** ne sont pas incluses dans les calculs de votre degré de sécurisation. Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommandation portant l’indicateur Preview (Préversion)":::

## <a name="improve-your-secure-score"></a>Améliorer votre score de sécurité

Pour améliorer votre degré de sécurisation, appliquez les recommandations de sécurité qui figurent dans la liste des recommandations. Vous pouvez appliquer chaque recommandation manuellement à chaque ressource, ou à l’aide de l’option **Correction rapide !** (si celle-ci est disponible) pour appliquer rapidement une recommandation à un groupe de ressources. Pour plus d’informations, consultez [Appliquer des recommandations](security-center-remediate-recommendations.md).

Une autre façon d’améliorer votre score et de vous assurer que vos utilisateurs ne créent pas de ressources ayant un impact négatif sur votre score consiste à configurer les options « Appliquer » et « Refuser » sur les recommandations pertinentes. Pour plus d’informations, consultez [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Contrôles de sécurité et leurs recommandations

Le tableau ci-dessous liste les contrôles de sécurité d’Azure Security Center. Pour chaque contrôle, vous pouvez voir le nombre maximal de points que vous pouvez ajouter à votre degré de sécurisation si vous appliquez *toutes* les recommandations indiquées dans le contrôle, pour *toutes* vos ressources. 

L’ensemble de recommandations de sécurité fournies par Security Center est adapté aux ressources disponibles dans l’environnement de chaque organisation. Les recommandations peuvent être personnalisées davantage en [désactivant les stratégies](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) et en [exemptant des ressources spécifiques d’une recommandation](exempt-resource.md). 
 
Nous recommandons à chaque organisation d’examiner attentivement les initiatives Azure Policy qui lui ont été attribuées. 

> [!TIP]
> Pour plus d’informations sur l’examen et la modification de vos initiatives, consultez [Utilisation de stratégies de sécurité](tutorial-security-policy.md). 

Bien que l’initiative de sécurité par défaut de Security Center soit basée sur les meilleures pratiques et les normes du secteur, il existe des scénarios dans lesquels les recommandations intégrées répertoriées ci-dessous peuvent ne pas être entièrement adaptées à votre organisation. Par conséquent, il est parfois nécessaire d’ajuster l’initiative par défaut (sans compromettre la sécurité) pour s’assurer qu’elle est alignée sur les propres stratégies de votre organisation, les normes du secteur, les normes réglementaires et les benchmarks que vous êtes tenu de respecter.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Questions fréquentes (FAQ) sur le degré de sécurisation

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Si je n’applique que trois recommandations sur quatre dans un contrôle de sécurité, mon degré de sécurisation changera-t-il ?
Non. Il ne changera pas tant que vous n’aurez pas appliqué toutes les recommandations fournies pour une même ressource. Pour obtenir le score maximal d’un contrôle, vous devez appliquer toutes les recommandations de l’ensemble des ressources.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Si je désactive une recommandation dans ma stratégie car elle ne s’applique pas à mon cas, mon contrôle de sécurité sera-t-il respecté et mon degré de sécurisation sera-t-il mis à jour ?
Oui. Nous vous recommandons de désactiver les recommandations qui ne s’appliquent pas à votre environnement. Pour obtenir des instructions sur la désactivation d’une recommandation, consultez [Désactiver des stratégies de sécurité](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Si un contrôle de sécurité n’ajoute aucun point à mon degré de sécurisation, dois-je l’ignorer ?
Dans certains cas, vous verrez un score de contrôle maximal supérieur à zéro, mais son impact est égal à zéro. Lorsque le score incrémentiel relatif à la correction de ressources est négligeable, celui-ci est arrondi à zéro. N’ignorez pas ces recommandations, car elles offrent malgré tout des améliorations au niveau de la sécurité. La seule exception concerne le contrôle « Bonne pratique supplémentaire ». L’application de ces recommandations n’augmentera pas votre score, mais elle améliorera votre sécurité globale.

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit le degré de sécurisation, ainsi que les contrôles de sécurité qu’il introduit. Pour des informations connexes, consultez les articles suivants :

- [En savoir plus sur les différents éléments d’une recommandation](security-center-recommendations.md)
- [Découvrez comment appliquer les recommandations](security-center-remediate-recommendations.md)
- [Consulter les outils basés sur GitHub pour travailler par programmation avec un score sécurisé](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Accéder à votre degré de sécurisation et le suivre](secure-score-access-and-track.md)