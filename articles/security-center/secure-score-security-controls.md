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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: 4a777b7474ddda529d4f183aafbccda86ea3ca41
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801500"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Version améliorée du degré de sécurisation (préversion) dans Azure Security Center

## <a name="introduction-to-secure-score"></a>Présentation du degré de sécurisation

Azure Security Center a deux objectifs principaux : vous permettre de connaître votre niveau de sécurité actuel et vous aider à l’améliorer efficacement. Dans Security Center, l’élément central qui vous permet d’atteindre ces objectifs est le degré de sécurisation.

Security Center évalue continuellement vos ressources, vos abonnements et votre organisation en recherchant d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible. Utilisez ce score pour effectuer le suivi des processus et projets relatifs à la sécurité qui sont mis en place par votre organisation. 

La page dédié au degré de sécurisations de Security Center comprend les éléments suivants :

- **Le degré de sécurisation** : il est affiché sous la forme de pourcentage, mais les valeurs sous-jacentes sont également claires :

    [![Degré de sécurisation affiché sous la forme d’un pourcentage avec des valeurs sous-jacentes également claires](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Contrôles de sécurité** : chaque contrôle est un groupe logique de recommandations de sécurité associées et reflète vos surfaces d’attaque vulnérables. Un contrôle est un ensemble de recommandations de sécurité, avec des instructions qui vous permettent de les implémenter. Votre degré de sécurisation n’augmente que si vous avez suivi *toutes* les recommandations fournies pour une même ressource au sein d’un contrôle.

    Pour voir immédiatement dans quelle mesure votre organisation sécurise chacune des surfaces d’attaque, examinez le degré de chaque contrôle de sécurité.

    Pour plus d’informations, consultez [Mode de calcul du degré de sécurisation](secure-score-security-controls.md#how-the-secure-score-is-calculated), ci-dessous. 


>[!TIP]
> Les versions antérieures de Security Center attribuaient des points au niveau des recommandations : quand vous appliquiez une recommandation pour une seule ressource, votre degré de sécurisation était amélioré. Aujourd’hui, votre degré de sécurisation augmente seulement si vous avez suivi *toutes* les recommandations fournies pour une même ressource au sein d’un contrôle. Par conséquent, votre degré de sécurisation n’augmente que quand vous avez amélioré la sécurité d’une ressource.
> Cette version améliorée est toujours en préversion, mais l’expérience de degré de sécurisation précédente est disponible en tant qu’option dans le portail Azure. 


## <a name="locating-your-secure-score"></a>Localisation de votre degré de sécurisation

Security Center affiche le degré de sécurisation de manière bien visible, puisque c’est la première chose que l’on voit quand on ouvre la page Vue d’ensemble. Si vous cliquez sur la page dédiée au degré de sécurisation, vous verrez le degré de sécurisation global décomposé par abonnement. Cliquez sur un abonnement pour voir la liste détaillée des recommandations classées par ordre de priorité ainsi que l’impact que peut avoir leur prise en compte sur le degré de sécurisation de l’abonnement.

## <a name="how-the-secure-score-is-calculated"></a>Mode de calcul du degré de sécurisation 

La contribution de chaque contrôle de sécurité au degré de sécurisation global est indiquée clairement dans la page des recommandations.

[![La version améliorée du degré de sécurisation comprend désormais des contrôles de sécurité](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Pour obtenir le nombre maximal de points que peut avoir un contrôle de sécurité, toutes vos ressources doivent se conformer à l’intégralité des recommandations de sécurité fournies dans le contrôle de sécurité. Par exemple, Security Center fournit plusieurs recommandations relatives à la sécurisation de vos ports de gestion. Avant, vous pouviez appliquer certaines de ces recommandations connexes ou interdépendantes, et en ignorer d’autres, et voir malgré tout votre degré de sécurisation s’améliorer. Si l’on est objectif, on peut considérer que votre sécurité n’était pas améliorée tant que vous n’aviez pas appliqué toutes les recommandations. À présent, vous devez toutes les appliquer pour voir votre degré de sécurisation s’améliorer.

Par exemple, le contrôle de sécurité « Appliquer les mises à jour système » a un degré maximal de six points, que vous pouvez voir dans l’info-bulle concernant l’augmentation potentielle de la valeur du contrôle :

[![Contrôle de sécurité « Appliquer les mises à jour système »](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Dans la capture d’écran ci-dessus, l’augmentation potentielle de la valeur du contrôle de sécurité « Appliquer les mises à jour système » indique « 2 % (1 point) ». Cela signifie que si vous appliquez toutes les recommandations de ce contrôle, votre score augmentera de 2 % (dans ce cas, 1 point). Par souci de simplicité, les valeurs qui se trouvent dans la colonne « Augmentation potentielle » de la liste des recommandations sont arrondies à des nombres entiers. Les info-bulles affichent des valeurs précises :

* **Score maximal** - Nombre maximal de points que vous pouvez gagner en appliquant toutes les recommandations d’un contrôle. Le score maximal d’un contrôle indique l’importance relative de ce contrôle. Utilisez les valeurs de score maximal pour effectuer un triage des problèmes à traiter en priorité. 
* **Augmentation potentielle** : points restants à gagner dans le contrôle. Pour faire en sorte que ces points soient ajoutés à votre degré de sécurisation, appliquez toutes les recommandations du contrôle. Dans l’exemple ci-dessus, le point indiqué pour le contrôle correspond en réalité à 0,96 point.
* **Score actuel** : degré de sécurisation actuel du contrôle. Chaque contrôle contribue au score total. Dans cet exemple, le contrôle apporte une contribution de 5,04 points au score total. 

### <a name="calculations---understanding-your-score"></a>Calculs : comprendre votre score

|Métrique|Formule et exemple|
|-|-|
|**Degré de sécurisation actuel du contrôle de sécurité**|<br>![Équation pour le calcul du degré de sécurisation actuel d’un contrôle de sécurité](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Chaque contrôle de sécurité individuel contribue au degré de sécurisation global. Chaque ressource concernée par une recommandation dans le contrôle contribue au degré de sécurisation actuel du contrôle. Le degré de sécurisation actuel de chaque contrôle correspond à la mesure de l’état des ressources *comprises* dans ce contrôle.<br>![Info-bulles présentant les valeurs utilisées lors du calcul du degré de sécurisation actuel du contrôle de sécurité](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Dans cet exemple, le score (ou degré) maximal de 6 est divisé par 78, car il s’agit de la somme des ressources saines et non saines.<br>6 / 78 = 0,0769<br>En multipliant ce score par le nombre de ressources saines (4), vous obtenez le score actuel :<br>0,0769 * 4 = **0,31**<br><br>|
|**Degré de sécurisation**<br>Abonnement unique|<br>![Équation pour le calcul du degré de sécurisation actuel](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Degré de sécurisation d’un abonnement avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Dans cet exemple, vous pouvez voir un abonnement pour lequel tous les contrôles de sécurité sont disponibles (avec un degré de sécurisation maximal potentiel de 60 points). Le degré de sécurisation indique 28 points sur 60 points possibles, et les 32 points restants correspondent aux valeurs figurant dans la colonne « Potential score increase » (Augmentation potentielle du degré de sécurisation) des contrôles de sécurité.<br>![Liste des contrôles et augmentation potentielle du score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Degré de sécurisation**<br>Abonnements multiples|<br>Le degré de sécurisation actuel des ressources de tous les abonnements est ajouté, et le calcul est le même que pour un seul abonnement<br><br>Quand vous consultez plusieurs abonnements, le degré de sécurisation évalue toutes les ressources de toutes les stratégies activées, et regroupe leur impact combiné sur le degré maximal de chaque contrôle de sécurité.<br>![Degré de sécurisation pour plusieurs abonnements avec tous les contrôles activés](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Le score combiné **n’est pas** une moyenne. Il s’agit plutôt d’une évaluation de l’état de toutes les ressources de tous les abonnements.<br>Ici aussi, si vous accédez à la page des recommandations et si vous ajoutez les points que vous pouvez potentiellement gagner, vous constaterez qu’il s’agit de la différence entre le score actuel (24) et le score maximal possible (60).|
||||

## <a name="improving-your-secure-score"></a>Amélioration de votre degré de sécurisation

Pour améliorer votre degré de sécurisation, appliquez les recommandations de sécurité qui figurent dans la liste des recommandations. Vous pouvez appliquer chaque recommandation manuellement à chaque ressource, ou à l’aide de l’option **Correction rapide !** (si celle-ci est disponible) pour appliquer rapidement une recommandation à un groupe de ressources. Pour plus d’informations, consultez [Appliquer des recommandations](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Seules les recommandations intégrées ont un impact sur le degré de sécurisation.

## <a name="security-controls-and-their-recommendations"></a>Contrôles de sécurité et leurs recommandations

Le tableau ci-dessous liste les contrôles de sécurité d’Azure Security Center. Pour chaque contrôle, vous pouvez voir le nombre maximal de points que vous pouvez ajouter à votre degré de sécurisation si vous appliquez *toutes* les recommandations indiquées dans le contrôle, pour *toutes* vos ressources. 

> [!TIP]
> Si vous souhaitez filtrer ou trier cette liste différemment, copiez-la et collez-la dans Excel.

|Contrôle de sécurité|Nombre maximal de points du degré de sécurisation|Recommandations|
|----------------|:-------------------:|---------------|
|**Activer l’authentification multifacteur**|10|- L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement<br>- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement|
|**Sécuriser les ports de gestion**|8|- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles<br>- Les machines virtuelles doivent être associées à un groupe de sécurité réseau<br>- Les ports de gestion doivent être fermés sur vos machines virtuelles|
|**Appliquer des mises à jour système**|6|- Les problèmes d’intégrité de l’agent de supervision doivent être résolus sur vos machines<br>- L’agent de supervision doit être installé sur les groupes de machines virtuelles identiques<br>- L’agent de supervision doit être installé sur vos machines<br>- La version du système d’exploitation doit être mise à jour pour vos rôles de service cloud<br>- Les mises à jour système sur les groupes de machines virtuelles identiques doivent être installées<br>- Les mises à jour système doivent être installées sur vos machines<br>- Vos machines doivent être redémarrées pour appliquer les mises à jour système<br>- Les services Kubernetes doivent être mis à niveau vers une version non vulnérable de Kubernetes<br>- Un agent de supervision doit être activé sur vos machines virtuelles|
|**Corriger des vulnérabilités**|6|- Advanced Data Security doit être activé sur vos serveurs SQL<br>- Les vulnérabilités dans les images Azure Container Registry doivent être corrigées<br>- Les vulnérabilités sur vos bases de données SQL doivent être corrigées<br>- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités<br>- L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL<br>- L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL<br>- La solution d’évaluation des vulnérabilités doit être installée sur vos machines virtuelles|
|**Activer le chiffrement des données au repos**|4|- Le chiffrement de disque doit être appliqué sur les machines virtuelles<br>- Transparent Data Encryption sur les bases de données SQL doit être activé<br>- Les variables de compte Automation doivent être chiffrées<br>- La propriété ClusterProtectionLevel doit être définie sur EncryptAndSign pour les clusters Service Fabric<br>- Le protecteur TDE du serveur SQL doit être chiffré avec votre propre clé|
|**Chiffrer les données en transit**|4|- L’application API doit uniquement être accessible via HTTPS<br>- L’application de fonction ne doit pas être accessible via HTTPS<br>- Seules les connexions sécurisées à votre cache Redis doivent être activées<br>- La sécurisation du transfert vers des comptes de stockage doit être activée<br>- L’application web ne doit pas être accessible via HTTPS|
|**Gérer l’accès et les autorisations**|4|- Les comptes dépréciés doivent être supprimés de votre abonnement (préversion)<br>- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement (préversion)<br>- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement (préversion)<br>- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement (préversion)<br>- Plusieurs propriétaires doivent être attribués à votre abonnement<br>- Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sur les services Kubernetes (préversion)<br>- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client|
|**Corriger les configurations de sécurité**|4|- Des stratégies de sécurité des pods doivent être définies sur les services Kubernetes<br>- Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées<br>- Les vulnérabilités dans la configuration de la sécurité sur vos machines doivent être corrigées<br>- Les vulnérabilités dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées<br>- Un agent de supervision doit être activé sur vos machines virtuelles<br>- L’agent de supervision doit être installé sur vos machines<br>- L’agent de supervision doit être installé sur les groupes de machines virtuelles identiques<br>- Les problèmes d’intégrité de l’agent de supervision doivent être résolus sur vos machines|
|**Restreindre l’accès réseau non autorisé**|4|- Le transfert IP doit être désactivé sur votre machine virtuelle<br>- Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes (préversion)<br>- (DÉCONSEILLÉ) L’accès à App Services doit être restreint (préversion)<br>- (DÉCONSEILLÉ) Les règles relatives aux applications web doivent être renforcées sur les groupes de sécurité réseau IaaS<br>- Les machines virtuelles doivent être associées à un groupe de sécurité réseau<br>- CORS ne doit pas autoriser toutes les ressources à accéder à votre application API<br>- CORS ne doit pas autoriser toutes les ressources à accéder à votre application de fonction<br>- CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web<br>- Le débogage à distance doit être désactivé pour l’application API<br>- Le débogage à distance doit être désactivé pour l’application de fonction<br>- Le débogage à distance doit être désactivé pour les applications web<br>- L’accès doit être limité pour les groupes de sécurité réseau permissifs avec machines virtuelles connectées à Internet<br>- Les règles de groupe de sécurité réseau pour les machines virtuelles connectées à Internet doivent être renforcées|
|**Appliquer un contrôle d’application adaptatif**|3|- Des contrôles d’application adaptatifs doivent être activés sur les machines virtuelles<br>- L’agent de supervision doit être activé sur vos machines virtuelles<br>- L’agent de supervision doit être installé sur vos machines<br>- Les problèmes d’intégrité de l’agent de supervision doivent être résolus sur vos machines|
|**Appliquer la classification des données**|2|- Les données sensibles de vos bases de données SQL doivent être classifiées (préversion)|
|**Protéger les applications contre les attaques DDoS**|2|- DDoS Protection Standard doit être activé|
|**Activer Endpoint Protection**|2|- Les échecs d’intégrité Endpoint Protection doivent être corrigés sur les groupes de machines virtuelles identiques<br>- Les problèmes d’intégrité Endpoint Protection doivent être résolus sur vos machines<br>- La solution Endpoint Protection doit être installée sur les groupes de machines virtuelles identiques<br>- Installer la solution Endpoint Protection sur les machines virtuelles<br>- Les problèmes d’intégrité de l’agent de supervision doivent être résolus sur vos machines<br>- L’agent de supervision doit être installé sur les groupes de machines virtuelles identiques<br>- L’agent de supervision doit être installé sur vos machines<br>- L’agent de supervision doit être activé sur vos machines virtuelles<br>- Installer la solution Endpoint Protection sur vos machines|
|**Activer l’audit et la journalisation**|1|- L’audit sur SQL Server doit être activé<br>- Les journaux de diagnostic d’App Services doivent être activés<br>- Les journaux de diagnostic dans Azure Data Lake Store doivent être activés<br>- Les journaux de diagnostic dans Azure Stream Analytics doivent être activés<br>- Les journaux de diagnostic dans les comptes Batch doivent être activés<br>- Les journaux de diagnostic dans Data Lake Analytics doivent être activés<br>- Les journaux de diagnostic dans Event Hub doivent être activés<br>- Les journaux de diagnostic dans IoT Hub doivent être activés<br>- Les journaux de diagnostic dans Key Vault doivent être activés<br>- Les journaux de diagnostic dans Logic Apps doivent être activés<br>- Les journaux de diagnostic dans les services Search doivent être activés<br>- Les journaux de diagnostic dans Service Bus doivent être activés<br>- Les journaux de diagnostic dans les groupes de machines virtuelles identiques doivent être activés<br>- Des règles d’alerte de métrique doivent être configurées sur les comptes Batch<br>- Les paramètres d’audit SQL doivent avoir des groupes d’actions configurés pour capturer les activités critiques<br>- Les serveurs SQL doivent être configurés avec une période de conservation d’audit supérieure à 90 jours|
|**Implémenter les bonnes pratiques de sécurité**|0|- 3 propriétaires maximum doivent être désignés pour votre abonnement<br>- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement<br>- L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de lecture sur votre abonnement<br>- L’accès aux comptes de stockage avec des configurations de pare-feu et de réseau virtuel doit être limité<br>- Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Event Hub<br>- Un administrateur Azure Active Directory doit être provisionné pour les serveurs SQL<br>- Les règles d’autorisation sur l’instance Event Hub doivent être définies<br>- Les comptes de stockage doivent faire l’objet d’une migration vers de nouvelles ressources Azure Resource Manager<br>- Les machines virtuelles doivent faire l’objet d’une migration vers de nouvelles ressources Azure Resource Manager<br>- Les paramètres Advanced Data Security pour le serveur SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité<br>- Advanced Data Security doit être activé sur vos instances managées<br>- Tous les types Advanced Threat Protection doivent être activés dans les paramètres Advanced Data Security de l’instance managée SQL<br>- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security SQL Server<br>- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security du serveur SQL<br>- Les sous-réseaux doivent être associés à un groupe de sécurité réseau<br>- Tous les types Advanced Threat Protection doivent être activés dans les paramètres Advanced Data Security de SQL Server|
||||

## <a name="secure-score-faq"></a>Questions fréquentes (FAQ) sur le degré de sécurisation

### <a name="why-has-my-secure-score-gone-down"></a>Pourquoi mon degré de sécurisation a-t-il baissé ?
Security Center a basculé vers un degré de sécurisation amélioré (actuellement en préversion), ce qui inclut les changements de la façon dont le degré est calculé. À présent, vous devez appliquer toutes les recommandations concernant une ressource pour gagner des points. Les scores sont désormais calculés sur une échelle de 0 à 10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Si je n’applique que trois recommandations sur quatre dans un contrôle de sécurité, mon degré de sécurisation changera-t-il ?
Non. Il ne changera pas tant que vous n’aurez pas appliqué toutes les recommandations fournies pour une même ressource. Pour obtenir le score maximal d’un contrôle, vous devez appliquer toutes les recommandations de l’ensemble des ressources.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>L’expérience précédente du degré de sécurisation est-elle toujours disponible ? 
Oui. Elles s’exécuteront côte à côte pendant un temps afin de faciliter la transition. Attendez-vous à ce que le modèle précédent soit au final abandonné. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Si je désactive une recommandation dans ma stratégie car elle ne s’applique pas à mon cas, mon contrôle de sécurité sera-t-il respecté et mon degré de sécurisation sera-t-il mis à jour ?
Oui. Nous vous recommandons de désactiver les recommandations qui ne s’appliquent pas à votre environnement. Pour obtenir des instructions sur la désactivation d’une recommandation, consultez [Désactiver des stratégies de sécurité](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Si un contrôle de sécurité n’ajoute aucun point à mon degré de sécurisation, dois-je l’ignorer ?
Dans certains cas, vous verrez un score de contrôle maximal supérieur à zéro, mais son impact sera égal à zéro. Lorsque le score incrémentiel relatif à la correction de ressources est négligeable, celui-ci est arrondi à zéro. N’ignorez pas ces recommandations, car elles offrent malgré tout des améliorations au niveau de la sécurité. La seule exception concerne le contrôle « Bonne pratique supplémentaire ». L’application de ces recommandations n’augmentera pas votre score, mais elle améliorera votre sécurité globale.

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit le degré de sécurisation, ainsi que les contrôles de sécurité qu’il introduit. Pour des informations connexes, consultez les articles suivants :

- [En savoir plus sur les différents éléments d’une recommandation](security-center-recommendations.md)
- [Découvrez comment appliquer les recommandations](security-center-remediate-recommendations.md)