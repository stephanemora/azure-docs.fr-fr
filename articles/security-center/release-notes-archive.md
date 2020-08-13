---
title: Archive des nouveautés d’Azure Security Center
description: Description des nouveautés et modifications apportées à Azure Security Center jusqu’à il y a six mois.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: cebc2c3d364cdb93c4b528c186dcef79c58ac6ea
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167009"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archive des nouveautés d’Azure Security Center

La page principale des notes de publication [Nouveautés d’Azure Security Center](release-notes.md) a trait aux mises à jour des six derniers mois, tandis que celle-ci a trait à des mises à jour plus anciennes.

Cette page fournit des informations sur les points suivants :

- Nouvelles fonctionnalités
- Résolution des bogues
- Fonctionnalités dépréciées


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

Pour en savoir plus, consultez [Version améliorée du degré de sécurisation (préversion)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembre 2019

Les mises à jour en novembre sont les suivantes :
 - [Protection contre les menaces pour Azure Key Vault dans les régions d’Amérique du Nord (préversion)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [La protection contre les menaces pour Stockage Azure inclut le filtrage de la réputation des programmes malveillants](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatisation des workflows avec Azure Logic Apps (préversion)](#workflow-automation-with-logic-apps-preview)
 - [Disponibilité générale d’un correctif rapide pour les ressources en bloc](#quick-fix-for-bulk-resources-generally-available)
 - [Analyser des images conteneur pour détecter les vulnérabilités (préversion)](#scan-container-images-for-vulnerabilities-preview)
 - [Autres normes de conformité réglementaire (préversion)](#additional-regulatory-compliance-standards-preview)
 - [Protection contre les menaces pour Azure Kubernetes Service (préversion)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Évaluation des vulnérabilités des machines virtuelles (préversion)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security pour les serveurs SQL sur Machines virtuelles Microsoft Azure (préversion)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Prise en charge de stratégies personnalisées (préversion)](#support-for-custom-policies-preview)
 - [Extension de la couverture d’Azure Security Center avec une plateforme pour la communauté et les partenaires](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Intégrations avancées avec exportation de recommandations et d’alertes (préversion)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Intégrer des serveurs locaux à Security Center à partir du Centre d’administration Windows (préversion)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Protection contre les menaces pour Azure Key Vault dans les régions d’Amérique du Nord (préversion)

Azure Key Vault est un service essentiel pour la protection des données et l’amélioration des performances des applications cloud, offrant la possibilité de gérer de manière centralisée les clés, les secrets, les clés de chiffrement et les stratégies dans le cloud. Étant donné qu’Azure Key Vault stocke des données sensibles et stratégiques, ses coffres de clés et les données qui y sont stockées exigent une sécurité maximale.

La prise en charge par Azure Security Center de la protection d’Azure Key Vault contre les menaces fournit une couche supplémentaire de veille de sécurité qui détecte les tentatives inhabituelles et potentiellement nuisibles d’accès aux coffres de clés ou d’exploitation de ceux-ci. Cette nouvelle couche de protection permet aux clients de traiter les menaces pesant sur leurs coffres de clés sans être experts en sécurité, ainsi que de gérer des systèmes de surveillance de la sécurité. Cette fonctionnalité est en préversion publique dans les régions d’Amérique du Nord.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>La protection contre les menaces pour Stockage Azure inclut le filtrage de la réputation des logiciels malveillants

La protection contre les menaces pour le service Stockage Azure offre de nouvelles fonctionnalités de détection optimisées par les renseignements sur les menaces Microsoft, qui permettent de détecter les chargements de programmes malveillants sur le service Stockage Azure à partir d’une de réputation de hachage et des accès suspects en provenance d’un nœud de sortie Tor actif (proxy d’anonymisation). Vous pouvez désormais afficher les programmes malveillants détectés parmi les comptes de stockage à l’aide d’Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatisation des flux de travail avec Azure Logic Apps (préversion)

Les organisations dont la sécurité, l’informatique et les opérations sont gérées de manière centralisée implémentent des processus de flux de travail internes pour conduire les actions requises en leur sein en cas de détection d’incohérences dans leur environnement. Dans de nombreux cas, ces flux de travail étant des processus reproductibles, une automatisation peut considérablement simplifier leur exécution au sein de l’organisation.

Aujourd’hui, nous introduisons dans Security Center une nouvelle fonctionnalité qui permet aux clients de créer des configurations d’automatisation tirant parti d’Azure Logic Apps, et d’élaborer des stratégies qui les déclencheront automatiquement en fonction de résultats d’ASC spécifiques, tels que des recommandations ou des alertes. Il est possible de configurer une application logique pour effectuer toute action personnalisée prise en charge par le vaste éventail de connecteurs d’applications logiques, ou d’utiliser l’un des modèles fournis par Security Center, tels que l’envoi d’un e-mail ou l’ouverture d’un ticket ServiceNow™.

Pour plus d’informations sur les fonctionnalités automatiques et manuelles d’Azure Security Center disponibles pour l’exécution de vos flux de travail, consultez [Automatisation des workflows](workflow-automation.md).

Pour en savoir plus sur la création d’applications logiques, consultez [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Disponibilité générale d’un correctif rapide pour les ressources en bloc

Compte tenu des nombreuses tâches confiées aux utilisateurs en lien avec le degré de sécurisation, il peut devenir difficile de résoudre efficacement les problèmes survenant au sein d’un part informatique de grande taille.

Pour simplifier la correction des configurations de sécurité incorrectes et appliquer rapidement des recommandations à un lot de ressources afin d’améliorer votre degré de sécurisation, utilisez une remédiation par correctif rapide.

Cette opération vous permet de sélectionner les ressources auxquelles à corriger et de lancer une action de correction qui configure le paramètre à votre place.

Un correctif rapide est dès aujourd’hui en disponibilité générale pour les clients dans la page Recommandations du Security Center.

Pour connaître les recommandations assorties d’un correctif rapide, consultez le [guide de référence relatif aux recommandations de sécurité](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Analyser des images de conteneur pour détecter des vulnérabilités (préversion)

Azure Security Center peut désormais analyser des images de conteneur dans Azure Container Registry pour rechercher des vulnérabilités.

Le balayage d’image fonctionne en analysant le fichier image du conteneur, puis en vérifiant s’il existe des vulnérabilités connues (optimisées par Qualys).

L’analyse proprement dite est déclenchée automatiquement lors de l’envoi (push) de nouvelles images de conteneur au Registre de conteneurs Azure (Azure Container Registry). Les vulnérabilités détectées sont présentées en tant que recommandations d’Azure Security Center, et incluses dans le degré de sécurisation Azure avec des informations sur la façon de les corriger afin de réduire la surface d’attaque autorisée.


### <a name="additional-regulatory-compliance-standards-preview"></a>Normes de conformité réglementaire supplémentaires (préversion)

Le tableau de bord Conformité réglementaire fournit des insights sur votre posture de conformité en fonction des évaluations du Security Center. Le tableau de bord indique la conformité de votre environnement avec des contrôles et exigences stipulés par des normes réglementaires et des benchmarks sectoriels spécifiques, et fournit des recommandations prescriptives sur la façon de s’y conformer.

À ce jour, ce tableau de bord prend en charge quatre normes intégrées :  Azure CIS 1.1.0, PCI-DSS, ISO 27001 et SOC-TSP. Nous annonçons à présent la pris en charge en préversion publique de normes supplémentaires, à savoir : NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM et UK Official avec UK NHS. Nous publions également une version mise à jour d’Azure CIS 1.1.0, qui couvre davantage de contrôles à partir de l’extensibilité standard et améliorée.

[Apprenez-en davantage sur la personnalisation de l’ensemble de normes de votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Protection contre les menaces pour Azure Kubernetes Service (préversion)

Kubernetes devient rapidement la nouvelle norme pour le déploiement et la gestion de logiciels dans le cloud. Rares sont les utilisateurs qui ont une expérience approfondie de Kubernetes et nombreux sont ceux qui se concentrent uniquement sur l’ingénierie et l’administration générales en négligeant la sécurité. Il convient de configurer l’environnement Kubernetes avec soin pour le sécuriser en s’assurant qu’aucune porte laissée ouverte, donnant sur une surface d’attaque de conteneurs, n’est exposée pour des attaquants. Azure Security Center étend son support en lien avec les conteneurs à un service Azure qui connaît une croissance des plus rapides, à savoir Azure Kubernetes Service (AKS).

Les nouvelles fonctionnalités de cette préversion publique sont les suivantes :

- **Détection et visibilité** : détection continue des instances AKS gérées à l’intérieur des abonnements inscrits de Security Center.
- **Recommandations concernant le degré de sécurisation** : éléments actionnables pour aider les clients à se conformer aux meilleures pratiques de sécurité dans AKS en lien avec le degré de sécurisation du client, par exemple : « Le contrôle d’accès en fonction du rôle doit être utilisé pour limiter l’accès à un cluster Kubernetes Service ».
- **Détection des menaces** : analyses basées sur un hôte ou un cluster, par exemple, « un conteneur privilégié détecté ».


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Évaluation des vulnérabilités des machines virtuelles (préversion)

Les applications installées sur les machines virtuelles peuvent souvent présenter des vulnérabilités susceptibles d’entraîner une violation de ces machines. Nous annonçons que le niveau de service Standard de Security Center comprend une évaluation des vulnérabilités intégrée pour les machines virtuelles sans frais supplémentaires. L’évaluation des vulnérabilités, optimisée par Qualys dans la préversion publique, vous permet d’analyser en continu toutes les applications installées sur une machine virtuelle afin d’épingler celles qui sont vulnérables, et de présenter les résultats sur le portail Security Center. Security Center prend en charge toutes les opérations de déploiement afin que l’utilisateur n’ait aucun travail supplémentaire à accomplir. À l’avenir, nous envisageons de fournir des options d’évaluation des vulnérabilités pour satisfaire aux besoins uniques de nos clients.

[Apprenez-en davantage sur l’évaluation des vulnérabilités de vos machines virtuelles Azure](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security pour SQL Server sur machines virtuelles Azure (préversion)

Le support d’Azure Security Center en lien avec la protection contre les menaces et l’évaluation des vulnérabilités pour les bases de données SQL s’exécutant sur des machines virtuelles IaaS est désormais en préversion.

La fonctionnalité [Évaluation des vulnérabilités](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Il offre une visibilité sur votre posture de sécurité en lien avec le degré de sécurisation Azure, et inclut des étapes pour résoudre des problèmes de sécurité et renforcer la protection de votre base de données.

La [protection avancée contre les menaces](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) détecte des activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses d’accès à votre serveur SQL ou d’exploitation de celui-ci. Elle supervise en permanence votre base de données pour détecter des activités suspectes, et envoie des alertes de sécurité orientées action en cas de modèles d’accès anormaux à la base de données. Ces alertes fournissent des détails sur les activités suspectes et des mesures recommandées pour examiner et atténuer la menace.


### <a name="support-for-custom-policies-preview"></a>Prise en charge de stratégies personnalisées (préversion)

Azure Security Center prend désormais en charge les stratégies personnalisées (en préversion).

Nos clients ont exprimé le souhait d’étendre la couverture de leurs évaluations de la sécurité actuelles dans Security Center en y ajoutant leurs propres évaluations de la sécurité, basées sur des stratégies qu’ils créent dans Azure Policy. Grâce à la prise en charge des stratégies personnalisées, c’est désormais possible.

Ces stratégies personnalisées font désormais partie des recommandations d’Azure Security Center, du degré de sécurisation et du tableau de bord des normes de conformité réglementaire. Avec la prise en charge des stratégies personnalisées, vous pouvez désormais créer une initiative personnalisée dans Azure Policy, puis l’ajouter en tant que stratégie dans Azure Security Center et la visualiser en tant que recommandation.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Extension de la couverture d’Azure Security Center avec une plateforme pour la communauté et les partenaires

Utilisez Security Center pour recevoir des recommandations, non seulement de Microsoft, mais aussi de solutions existantes de partenaires tels que Check Point, Tenable et CyberArk, avec de nombreuses intégrations supplémentaires à venir.  Le flux d’intégration simple de Security Center peut connecter vos solutions existantes à Security Center, ce qui vous permet d’afficher les recommandations relatives à votre posture de sécurité dans un emplacement unique, de générer des rapports unifiés et de tirer parti de toutes les fonctionnalités de Security Center en lien avec les recommandations intégrées et de partenaires. Vous pouvez également exporter les recommandations de Security Center vers des produits partenaires.

[Apprenez-en davantage sur l’Association de sécurité intelligente de Microsoft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Intégrations avancées avec exportation de recommandations et d’alertes (préversion)

Afin d’activer des scénarios de niveau entreprise en plus de Security Center, il est désormais possible d’utiliser des alertes et recommandations du Security Center dans des emplacements supplémentaires, à l’exception du portail Azure ou de l’API. Vous pouvez les exporter directement vers un Event Hub et des espaces de travail Log Analytics. Voici quelques flux de travail que vous pouvez créer autour de ces nouvelles fonctionnalités :

- Avec une exportation vers un espace de travail Log Analytics, vous pouvez créer des tableaux de bord personnalisés avec Power BI.
- Avec une exportation vers Event Hub, vous pouvez exporter les alertes et recommandations d’Azure Security Center vers vos informations de sécurité et gestion d’événements (SIEM) tierces, vers une solution tierce en temps réel ou vers Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Intégrer des serveurs locaux à Security Center à partir du Centre d’administration Windows (préversion)

Le Centre d’administration Windows est un portail de gestion pour les serveurs Windows non déployés dans Azure, qui offre à ceux-ci plusieurs fonctionnalités de gestion Azure, telles que la sauvegarde et les mises à jour système. Nous avons récemment ajouté la possibilité d’intégrer ces serveurs non-Azure de façon à ce qu’ils soient protégés par ASC directement à partir du Centre d’administration Windows.

Avec cette nouvelle expérience, les utilisateurs devront intégrer un serveur WAC à Azure Security Center et activer l’affichage de des alertes et recommandations de sécurité de celui-ci directement dans le Centre d’administration Windows.


## <a name="september-2019"></a>Septembre 2019

Les mises à jour en septembre sont les suivantes :

 - [Gestion des règles avec des améliorations des contrôles d’application adaptatifs](#managing-rules-with-adaptive-application-controls-improvements)
 - [Contrôler la recommandation de sécurité de conteneur à l’aide d’Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gestion des règles avec des améliorations des contrôles d’application adaptatifs

L’expérience de gestion des règles pour les machines virtuelles à l’aide de contrôles d’application adaptatifs a été améliorée. Les contrôles d’application adaptatifs d’Azure Security Center vous aident à contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles. En plus d’une amélioration générale de la gestion des règles, un nouvel avantage vous permet de contrôler les types de fichiers qui seront protégés lorsque vous ajoutez une nouvelle règle.

[Apprenez-en davantage sur les contrôles d’application adaptatifs](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Contrôler la recommandation de sécurité de conteneur à l’aide d’Azure Policy

La recommandation d’Azure Security Center pour corriger des vulnérabilités dans la sécurité de conteneur peut désormais être activée ou désactivée via Azure Policy.

Pour afficher vos stratégies de sécurité activées, dans le Security Center, ouvrez la page Stratégie de sécurité.


## <a name="august-2019"></a>Août 2019

Les mises à jour en août sont les suivantes :

 - [Accès de machine virtuelle juste-à-temps (JAT) pour Pare-feu Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Correction en un seul clic pour améliorer votre posture de sécurité (préversion)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gestion multilocataire](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Accès de machine virtuelle juste-à-temps (JAT) pour le Pare-feu Azure 

L’accès de machine virtuelle juste-à-temps (JAT) pour le Pare-feu Azure est désormais généralement disponible. Utilisez-le pour sécuriser vos environnements protégés par un Pare-feu Azure en plus de vos environnements protégés par un groupe de sécurité réseau.

L’accès de machine virtuelle JAT réduit l’exposition aux attaques volumétriques de réseau en fournissant aux machines virtuelles un accès contrôlé uniquement si nécessaire, à l’aide de vos règles de groupe de sécurité réseau et de Pare-feu Azure.

Lorsque vous activez l’accès JAT pour vos machines virtuelles, vous créez une stratégie qui détermine les ports à protéger, la durée pendant laquelle les ports doivent rester ouverts et les adresses IP approuvées à partir desquelles ces ports sont accessibles. Cette stratégie vous permet contrôler ce que les utilisateurs peuvent faire quand ils demandent l’accès.

Les demandes étant consignées dans le journal d’activité Azure, vous pouvez surveiller et vérifier facilement l’accès. La page juste-à-temps vous aide également à identifier rapidement les machines virtuelles existantes pour lesquelles l’accès JAT est activé et celles pour lesquelles il est recommandé.

[Apprenez-en davantage sur le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correction en un seul clic pour améliorer votre posture de sécurité (préversion)

Le degré de sécurisation est un outil qui vous aide à évaluer la sécurité de la charge de travail. Il examine vos recommandations de sécurité et les hiérarchise de façon à ce que vous sachiez celles que vous devez appliquer en premier. Cela vous aide à épingler les vulnérabilités de sécurité les plus graves pour hiérarchiser l’investigation.

Afin de simplifier la correction des configurations de sécurité incorrectes et de vous aider à améliorer rapidement votre degré de sécurisation, nous avons ajouté une nouvelle fonctionnalité qui vous permet d’appliquer une recommandation à un lot de ressources en un seul clic.

Cette opération vous permettra de sélectionner les ressources auxquelles vous souhaitez appliquer la correction, et de lancer une action de correction qui configurera le paramètre pour vous.

Pour connaître les recommandations assorties d’un correctif rapide, consultez le [guide de référence relatif aux recommandations de sécurité](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gestion multilocataire

Azure Security Center prend désormais en charge les scénarios de gestion mutualisée dans Azure Lighthouse. Cela vous permet d’acquérir une visibilité et de gérer la posture de sécurité de plusieurs locataires dans Azure Security Center. 

[Apprenez-en davantage sur les expériences de gestion mutualisée](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juillet 2019

### <a name="updates-to-network-recommendations"></a>Mises à jour des recommandations pour le réseau

Azure Security Center a publié de nouvelles recommandations pour la mise en réseau, et amélioré des recommandations existantes. Désormais, l’utilisation d’Azure Security Center garantit une meilleure protection réseau pour vos ressources. 

[Apprenez-en davantage sur les recommandations pour le réseau](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Juin 2019

### <a name="adaptive-network-hardening---generally-available"></a>Disponibilité générale du renforcement du réseau adaptatif

L’une des principales surfaces d’attaque pour les charges de travail s’exécutant dans le cloud public est celle des interconnexions avec l’Internet public. Nos clients trouvent difficile de savoir quelles règles de groupe de sécurité mettre en place pour s’assurer que les charges de travail Azure ne sont disponibles que pour les plages sources requises. Cette fonctionnalité permet à Security Center d’apprendre le trafic réseau et les modèles de connectivité des charges de travail Azure, et fournit des recommandations de règle de groupe de sécurité réseau pour les machines virtuelles accessibles via Internet. Cela permet à nos clients de mieux configurer leurs stratégies d’accès réseau et de limiter leur exposition aux attaques. 

[Apprenez-en davantage sur le renforcement du réseau adaptative](security-center-adaptive-network-hardening.md).
