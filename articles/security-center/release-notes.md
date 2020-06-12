---
title: Notes de publication pour Azure Security Center
description: Description des nouveautés et modifications apportées à Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 3fa9de1057160340fdf10d72809a104cae985162
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248156"
---
# <a name="whats-new-in-azure-security-center"></a>Nouveautés d’Azure Security Center

Azure Security fait l’objet d’un développement actif et bénéficie d’améliorations en permanence. Pour vous tenir au courant des développements les plus récents, cette page fournit des informations sur les points suivants :

- Nouvelles fonctionnalités
- Résolution des bogues
- Fonctionnalités dépréciées

Cette page est mise à jour régulièrement. Nous vous invitons donc à la consulter souvent. Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Security Center](release-notes-archive.md).


## <a name="may-2020"></a>Mai 2020


### <a name="alert-suppression-rules-preview"></a>Règles de suppression d’alerte (préversion)

Cette nouvelle fonctionnalité (actuellement en préversion) permet de réduire la fréquence des alertes. Utilisez des règles pour masquer automatiquement les alertes connues pour être anodines ou liées à des activités normales au sein votre organisation. Cela vous permet de vous concentrer sur les menaces les plus pertinentes. 

Les alertes correspondant à vos règles de suppression activées sont toujours générées, mais leur état est défini sur ignoré. Vous pouvez voir leur état sur le portail Azure ou en accédant aux alertes de votre Security Center.

Les règles de suppression définissent les critères en vertu desquels les alertes doivent être automatiquement ignorées. En règle générale, vous utilisez une règle de suppression pour effectuer les opérations suivantes :

- supprimer des alertes que vous avez identifiées comme étant des faux positifs ;

- supprimer des alertes déclenchées trop souvent pour être utiles.

Apprenez-en davantage sur la [suppression des alertes à partir de la protection contre les menaces d’Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Disponibilité de l’évaluation des vulnérabilités des machines virtuelles

Le niveau standard de Security Center intègre désormais l’évaluation des vulnérabilités des machines virtuelles sans frais supplémentaires. Cette extension est fournie par Qualys mais renvoie ses résultats directement à Security Center. Vous n’avez pas besoin d’une licence Qualys ni même de compte Qualys : tout est traité de manière fluide dans Security Center.

La nouvelle solution peut analyser en continu vos machines virtuelles pour trouver des vulnérabilités et présenter les résultats au Security Center. 

Pour déployer la solution, suivez la nouvelle recommandation de sécurité :

« Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (optimisées par Qualys) (Préversion)

Apprenez-en davantage sur l’[évaluation des vulnérabilités des machines virtuelles intégrée dans le Security Center](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Modifications apportées à l’accès juste-à-temps (JAT) des machines virtuelles

Le Security Center intègre une fonctionnalité facultative destinée à protéger les ports de gestion de vos machines virtuelles. Celle-ci offre une protection contre la forme la plus courante d’attaques par force brute.

Cette mise à jour apporte à cette fonctionnalité les modifications suivantes :

- La recommandation suggérant d’activer l’accès JAT sur une machine virtuelle a été reformulée. L’ancien libellé, « Un contrôle d’accès réseau juste-à-temps doit être appliqué aux machines virtuelles », est remplacé par « Les ports de gestion des machines virtuelles doivent être protégés par un contrôle d’accès réseau juste-à-temps ».

- La recommandation a été définie pour n’être déclenchée que s’il existe des ports de gestion ouverts.

Apprenez-en davantage sur la [fonctionnalité accès JAT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Déplacement des recommandations personnalisées vers un contrôle de sécurité distinct

L’un des contrôles de sécurité introduits par le degré de sécurisation amélioré était « Implémenter les meilleures pratiques de sécurité ». Toutes les recommandations personnalisées créées pour vos abonnements ont été placées automatiquement dans ce contrôle. 

Pour faciliter la recherche de vos recommandations personnalisées, nous les avons déplacées vers un contrôle de sécurité dédié nommé « Recommandations personnalisées ». Ce contrôle n’a aucun impact sur votre degré de sécurisation.

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Ajout d’une option permettant d’afficher les recommandations dans les contrôles ou sous forme de liste plate

Les contrôles de sécurité sont des groupes logiques de recommandations de sécurité associées. Ceux-ci reflètent vos surfaces d’attaque vulnérables. Un contrôle est un ensemble de recommandations de sécurité, avec des instructions qui vous permettent de les implémenter.

Pour voir immédiatement dans quelle mesure votre organisation sécurise chacune des surfaces d’attaque, examinez le degré de chaque contrôle de sécurité.

Par défaut, vos recommandations s’affichent dans les contrôles de sécurité. À partir de cette mise à jour, vous pouvez également les afficher sous forme de liste. Pour les afficher sous la forme d’une liste simple triée en fonction de l’état d’intégrité des ressources affectées, utilisez la nouvelle option « Grouper par contrôles ». Cette option se trouve au-dessus de la liste dans le portail.

Les contrôles de sécurité, et cette option, font partie de la nouvelle expérience de degré de sécurisation. N’oubliez pas de nous envoyer vos commentaires à partir du portail.

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).


### <a name="expanded-security-control-implement-security-best-practices"></a>Extension du contrôle de sécurité « Implémenter les bonnes pratiques de sécurité » 

L’un des contrôles de sécurité introduits avec le degré de sécurisation amélioré est « Implémenter les bonnes pratiques de sécurité ». Quand ce contrôle contient une recommandation, celle-ci n’a aucun impact sur le degré de sécurisation. 

Avec cette mise à jour, trois recommandations ont été déplacées des contrôles dans lesquels elles étaient placées à l’origine vers ce contrôle des bonnes pratiques. Nous avons pris cette mesure parce que nous avons constaté que le risque que ces trois recommandations visaient à prévenir était moindre que le risque initialement prévu.

En outre, deux nouvelles recommandations ont été introduites et ajoutées à ce contrôle.

Les trois recommandations déplacées sont les suivantes :

- **L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement** (à l’origine, dans le contrôle « Activer la MFA »).
- **Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement** (à l’origine, dans le contrôle « Gérer l’accès et les autorisations »).
- **Trois propriétaires au plus doivent être désignés pour votre abonnement** (à l’origine, dans le contrôle « Gérer l’accès et les autorisations »).

Les deux nouvelles recommandations ajoutées au contrôle sont les suivantes :

- **[Préversion] L’agent de configuration invité doit être installé** : la [Configuration d’invité Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) apporte une visibilité des machines virtuelles aux paramètres de serveur et d’application (Windows uniquement).

- **[Préversion] Windows Defender Exploit Guard doit être activé** : Windows Defender Exploit Guard tire parti de l’agent de configuration invité Azure Policy. Windows Defender Exploit Guard compte quatre composants conçus pour verrouiller les appareils afin de les protéger contre un vaste éventail de vecteurs d’attaque et comportements de blocage couramment utilisés par les programmes malveillants, tout en permettant aux entreprises de trouver un juste équilibre entre sécurité et productivité (Windows uniquement).

Pour en savoir plus sur Windows Defender exploit Guard, consultez [Créer et déployer une stratégie Windows Defender Exploit Guard](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Disponibilité générale des stratégies personnalisées avec des métadonnées personnalisées

Les stratégies personnalisées font désormais partie de l’expérience des Recommandations relatives à Azure Security Center, du degré de sécurisation et du tableau de bord des normes de conformité réglementaire. Cette fonctionnalité désormais généralement disponible vous permet d’étendre la couverture de l’évaluation de la sécurité de votre organisation dans Azure Security Center. 

Créez une initiative personnalisée dans Azure Policy, ajoutez-y des stratégies, intégrez-la à Azure Security Center et visualisez-la sous la forme de recommandations.

Nous avons également ajouté une option permettant de modifier les métadonnées de recommandation personnalisées. Les options des métadonnées incluent la gravité, des mesures de correction, des informations sur les menaces, etc.  

Pour en savoir plus, consultez [Amélioration de vos recommandations personnalisées avec des informations détaillées](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migration des fonctionnalités d’analyse du vidage sur incident vers la détection d’attaque sans fichier 

Nous intégrons les fonctionnalités de détection de l’analyse du vidage sur incident Windows dans la [détection d’attaque sans fichier](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). L’analyse de la détection d’attaque sans fichier offre des versions améliorées des alertes de sécurité suivantes pour les ordinateurs Windows : Injection de code découverte, usurpation d’identité de module Windows détectée, Shellcode détecté et segment de code suspect détecté.

Voici quelques-uns des avantages de cette transition :

- **Détection proactive et en temps opportun des programmes malveillants** : l’approche de l’analyse du vidage sur incident impliquait l’attente de la survenance d’un incident, puis l’exécution d’une analyse pour trouver des artefacts malveillants. La détection d’attaque sans fichier introduit l’identification de manière proactive des menaces en mémoire pendant leur exécution. 

- **Alertes enrichies** : les alertes de sécurité liées à la détection d’attaque sans fichier apportent des enrichissements par rapport à une simple analyse du vidage sur incident, tels que les informations de connexions réseau actives. 

- **Agrégation d’alertes** : quand l’analyse du vidage sur incident détectait plusieurs modèles d’attaque au sein d’un même vidage sur incident, elle déclenchait plusieurs alertes de sécurité. La détection d’attaque sans fichier combine tous les modèles d’attaque identifiés participant d’un même processus dans une alerte unique, ce qui évite d’avoir à mettre en corrélation plusieurs alertes.

- **Exigences réduites concernant votre espace de travail Log Analytics** : les vidages sur incident contenant des données potentiellement sensibles ne sont plus chargés dans votre espace de travail Log Analytics.



## <a name="april-2020"></a>Avril 2020

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Disponibilité générale des packages de conformité dynamique

Le tableau de bord de conformité réglementaire d’Azure Security Center inclut désormais des **packages de conformité dynamique** (en disponibilité générale) pour suivre des normes réglementaires et sectorielles supplémentaires.

Vous pouvez ajouter ces packages de conformité dynamique à votre abonnement ou à votre groupe d’administration à partir de la page Stratégie de sécurité d’Azure Security Center. Après l’intégration d’une norme ou un benchmark, ceux-ci apparaissent dans votre tableau de bord de conformité réglementaire avec toutes les données de conformité associées en tant qu’évaluations. Un rapport de synthèse pour toutes les normes intégrées sera disponible en téléchargement.

Désormais, vous pouvez ajouter des normes telles que les suivantes :

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official et UK NHS**
- **PBMM fédéral du Canada**
- **Azure CIS 1.1.0 (nouveau)** (représentation plus complète d’Azure CIS 1.1.0)

De plus, nous avons récemment ajouté le **Benchmark de sécurité Azure**, les directives spécifiques d’Azure créées par Microsoft pour les meilleures pratiques de sécurité et de conformité, basées sur des infrastructures de conformité courantes. Des normes supplémentaires seront prises en charge dans le tableau de bord dès qu’elles seront disponibles.  
 
Apprenez-en davantage sur la [personnalisation de l’ensemble de normes de votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Inclusion des recommandations relatives aux identités dans le niveau gratuit d’Azure Security Center

Les recommandations de sécurité relatives aux identités et aux accès sont désormais généralement disponibles dans le niveau gratuit d’Azure Security Center. Cela fait partie de l’effort visant à atteindre la gratuité des fonctionnalités de gestion de la posture de sécurité cloud. Jusqu’à présent, ces recommandations n’étaient disponibles qu’au niveau tarifaire standard.

Voici des exemples de recommandations relatives aux identités et aux accès :

- « L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement. »
- « Au maximum trois propriétaires doivent être désignés pour votre abonnement. »
- « Les comptes déconseillés doivent être supprimés de votre abonnement. »

Si vous avez des abonnements au niveau tarifaire gratuit, ce changement affectera leurs degrés de sécurisation, car ils n’ont jamais été évalués sur le plan de la sécurité des identités et des accès.

Apprenez-en davantage sur les [recommandations relatives aux identités et aux accès](recommendations-reference.md#recs-identity).

Apprenez-en davantage sur la [surveillance des identités et des accès](security-center-identity-access.md).


## <a name="march-2020"></a>Mars 2020

### <a name="workflow-automation-is-now-generally-available"></a>Disponibilité générale de l’automatisation de flux de travail

La fonctionnalité d’automatisation de flux de travail d’Azure Security Center est désormais généralement disponible. Elle permet de déclencher automatiquement Logic Apps sur des alertes et recommandations de sécurité. En outre, des déclencheurs manuels sont disponibles pour les alertes et toutes les recommandations pour lesquelles l’option de correction rapide est disponible.

Chaque programme de sécurité comprend plusieurs workflows pour la réponse aux incidents. Ces processus peuvent inclure l’envoi de notifications aux parties prenantes concernées, le lancement d’un processus de gestion des changements et l’application d’étapes de correction spécifiques. Les experts en sécurité vous conseillent d’automatiser le plus possible les étapes de ces processus. L’automatisation contribue à réduire la surcharge et à renforcer votre sécurité en garantissant que les étapes du processus se déroulent rapidement, de manière cohérente et selon les exigences que vous avez prédéfinies.

Pour plus d’informations sur les fonctionnalités automatiques et manuelles d’Azure Security Center pour l’exécution de vos flux de travail, consultez [Automatisation des workflows](workflow-automation.md).

Apprenez-en davantage sur la [création de Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Intégration d’Azure Security Center avec le Centre d’administration Windows

Vous pouvez désormais déplacer vos serveurs Windows locaux du Centre d’administration Windows directement vers Azure Security Center. Azure Security Center devient alors votre unique fenêtre pour l’affichage des informations de sécurité de toutes vos ressources du Centre d’administration Windows, à savoir les serveurs locaux, les machines virtuelles et les charges de travail PaaS supplémentaires.

Après avoir déplacé un serveur du Centre d’administration Windows vers Azure Security Center, vous pourrez effectuer les opérations suivantes :

- Afficher les alertes et recommandations de sécurité dans l’extension Security Center du Centre d’administration Windows.
- Afficher la posture de sécurité et des informations détaillées supplémentaires sur vos serveurs gérés par le Centre d’administration Windows dans le Security Center à l’intérieur du portail Azure (ou via une API).

Apprenez-en davantage sur la [façon d’intégrer Azure Security Center avec le Centre d’administration Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Protection pour Azure Kubernetes Service

Azure Security Center développe ses fonctionnalités de sécurité de conteneur pour protéger Azure Kubernetes Service (AKS).

La plateforme open source populaire Kubernetes a été adoptée si largement qu’elle fait désormais figure de norme sectorielle pour l’orchestration de conteneurs. En dépit de cette implémentation largement répandue, il subsiste un manque de compréhension de la manière de sécuriser un environnement Kubernetes. La défense des surfaces d’attaque d’une application en conteneur requiert de l’expertise pour s’assurer que l’infrastructure est configurée de façon totalement sécurisée et constamment surveillée pour détecter des menaces potentielles.

La défense orchestrée par Azure Security Center comprend les composantes suivantes :

- **Détection et visibilité** : détection continue des instances AKS gérées à l’intérieur des abonnements inscrits auprès d’Azure Security Center.
- **Recommandations de sécurité** : recommandations actionnables pour vous aider à vous conformer aux meilleures pratiques en matière de sécurité pour AKS. Ces recommandations sont incluses dans votre degré de sécurisation pour garantir leur visibilité en lien avec la posture de sécurité de votre organisation. Voici un exemple de recommandation relative à AKS : « Le contrôle d’accès en fonction du rôle doit être utilisé pour limiter l’accès à un cluster Kubernetes Service ».
- **Protection contre les menaces** : grâce à une analyse continue de votre déploiement AKS, Azure Security Center vous avertit des menaces et activités malveillantes détectées au niveau de l’hôte et du cluster AKS.

Pour en savoir plus, consultez [Intégration d’Azure Kubernetes Service avec Security Center](azure-kubernetes-service-integration.md).

Apprenez-en davantage sur les [fonctionnalités de sécurité de conteneur d’Azure Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Amélioration de l’expérience juste-à-temps

Les fonctionnalités, le fonctionnement et l’interface utilisateur des outils juste-à-temps de l’Azure Security Center qui sécurisent vos ports de gestion ont été améliorés comme suit : 

- **Champ de justification** : lors de la demande d’accès à une machine virtuelle via la page Juste-à-temps du portail Azure, un nouveau champ facultatif est disponible pour entrer une justification de la demande. Le journal d’activité permet de suivre les informations entrées dans ce champ. 
- **Nettoyage automatique des règles JAT redondantes** : chaque fois que vous mettez à jour une stratégie JAT, un outil de nettoyage s’exécute automatiquement pour vérifier la validité de votre ensemble de règles. L’outil recherche les incompatibilités entre les règles de votre stratégie et les règles du groupe de sécurité réseau. Si l’outil de nettoyage détecte une incompatibilité, il en détermine la cause et, lorsque cela ne présente aucun risque, supprime les règles intégrées qui ne sont plus nécessaires. Le nettoyeur ne supprime jamais les règles que vous avez créées. 

Apprenez-en davantage sur la [fonctionnalité d’accès JAT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Deux recommandations de sécurité pour les applications web déconseillées

Deux recommandations de sécurité relatives aux applications web sont déconseillées : 

- Les règles relatives aux applications web sur des groupes de sécurité réseau IaaS doivent être renforcées.
    (Stratégie associée : Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées)

- L’accès à App Services doit être limité.
    (Stratégie associée : L’accès à App Services doit être restreint [préversion])

Ces recommandations n’apparaissent plus dans la liste de recommandations d’Azure Security Center. Les stratégies associées ne seront plus incluses dans l’initiative nommée « Security Center par défaut ».

Apprenez-en davantage sur les [recommandations de sécurité](recommendations-reference.md).



## <a name="february-2020"></a>Février 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Détection d’attaque sans fichier pour Linux (préversion)

À mesure que les attaquants intensifient le recours à des méthodes de plus en plus furtives pour éviter d’être détectés, Azure Security Center étend la détection d’attaque sans fichier pour Linux, en plus de Windows. Les attaques sans fichier exploitent des vulnérabilités logicielles, injectent des charges utiles malveillantes dans des processus système inoffensifs et se cachent en mémoire. Ces techniques sont les suivantes :

- réduire ou éliminer les traces de logiciels malveillants sur disque ;
- réduire considérablement les risques de détection par des solutions d’analyse de programmes malveillants sur disque.

Pour contrer cette menace, Azure Security Center a publié une fonctionnalité de détection d’attaque sans fichier pour Windows en octobre 2018, et a maintenant étendu la détection d’attaque sans fichier également sur Linux. 


## <a name="january-2020"></a>Janvier 2020

### <a name="enhanced-secure-score-preview"></a>Amélioration du degré de sécurisation (préversion)

Une version améliorée de la fonctionnalité de degré de sécurisation d’Azure Security Center est désormais disponible en préversion. Dans cette version, plusieurs recommandations sont regroupées en contrôles de sécurité qui reflètent mieux vos surfaces d’attaque vulnérables (et, par exemple, restreignent l’accès aux ports de gestion).

Familiarisez-vous avec les changements apportés au degré de sécurisation au cours de la phase de préversion, et épinglez d’autres corrections qui vous aideront à sécuriser davantage votre environnement.

Pour en savoir plus, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).