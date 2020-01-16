---
title: Planifier une application RH cloud pour l’approvisionnement d’utilisateurs Azure Active Directory
description: Cet article décrit le processus de déploiement de l’intégration des systèmes RH cloud, tels que Workday et SucessFactors avec Azure Active Directory. L’intégration d’Azure AD à votre système RH cloud permet d’obtenir un système complet de gestion du cycle de vie des identités.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 6f72371077aab813cc22c9bbbe755fdfaa9ac00a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433836"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planifier une application RH cloud pour l’approvisionnement d’utilisateurs Azure Active Directory

Historiquement, le personnel informatique s’est appuyé sur des méthodes manuelles de création, de mise à jour et de suppression des employés, telles que le téléchargement de fichiers CSV ou les scripts personnalisés pour synchroniser les données des employés. Ces processus d’approvisionnement sont sujets aux erreurs ; ils sont non sécurisés et difficiles à gérer.

Pour gérer de façon transparente le cycle de vie des identités de bout en bout des employés, des fournisseurs ou des travailleurs occasionnels, [ le service d’approvisionnement d’utilisateurs Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) offre une intégration avec les applications de ressources humaines (RH) informatiques, telles que Workday ou SuccessFactors.

Azure AD utilise cette intégration pour activer les workflows suivants d’applications RH cloud :

- **Approvisionnement d’utilisateurs dans AD** : attribuez les ensembles d’utilisateurs sélectionnés dans une application RH cloud à un ou plusieurs domaines Active Directory (AD).
- **Approvisionnement d’utilisateurs cloud uniquement dans Azure AD** : dans les scénarios où AD n’est pas utilisé, approvisionnez des utilisateurs directement à partir de l’application RH cloud vers Azure AD.
- **Mise à jour de l’application RH cloud** : mettez à jour les adresses e-mail et les attributs de nom d’utilisateur à partir d’Azure AD sur l’application RH cloud.

> [!NOTE]
> Ce plan de déploiement explique comment déployer vos workflows d’application RH cloud à l’aide de l’approvisionnement d’utilisateurs Azure AD. Pour plus d’informations sur le déploiement de l’approvisionnement automatique d’utilisateurs vers des applications SaaS, consultez [Planifier un déploiement de l’approvisionnement automatique d’utilisateurs](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Scénarios RH activés

Le service d’approvisionnement d’utilisateurs Azure AD permet d’automatiser les scénarios suivants de gestion du cycle de vie des identités basée sur les RH :

- **Embauche de nouveaux employés** : lorsqu’un nouvel employé est ajouté à l’application RH cloud, un compte d’utilisateur est automatiquement créé dans AD et Azure AD avec la possibilité de mettre à jour l’adresse e-mail et les attributs du nom d’utilisateur dans l’application RH cloud.
- **Mises à jour du profil et des attributs de l’employé** : lorsque le dossier d’un employé est mis à jour dans l’application RH cloud (par exemple, son nom, son titre ou son responsable), son compte d’utilisateur est automatiquement mis à jour dans AD et Azure AD.
- **Licenciements d’employés** : lorsqu’un employé est licencié dans l’application RH cloud, son compte d’utilisateur est automatiquement désactivé dans AD et Azure AD.
- **Réembauche d’employés** : lorsqu’un employé est réembauché dans l’application RH cloud, son ancien compte peut être automatiquement réactivé ou réapprovisionné dans AD et Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>À qui cette intégration convient-elle le mieux ?

L’intégration de l’application RH cloud avec l’approvisionnement d’utilisateurs Azure AD est idéale pour les organisations qui :

- souhaitent une solution informatique prédéfinie pour l’approvisionnement d’utilisateurs RH cloud ;
- nécessitent un approvisionnement d’utilisateurs direct de l’application RH cloud vers AD ou Azure AD ;
- exigent que les utilisateurs soient approvisionnés à l’aide des données obtenues à partir de l’application RH cloud ;
- ont besoin de joindre, de déplacer et de laisser les utilisateurs à synchroniser avec une ou plusieurs unités d’organisation, domaines et forêts AD seulement sur la base d’informations de modifications détectées dans l’application RH cloud ;
- utilisent Office 365 pour les e-mails.

## <a name="learn"></a>Découvrir

L’attribution d’utilisateurs jette les bases d’une gouvernance des identités suivie, et améliore la qualité des processus métier qui s’appuient sur les données d’identité faisant autorité.

### <a name="terms"></a>Termes

Cet article utilise les termes suivants :

- **Système source** : référentiel des utilisateurs à partir duquel Azure AD approvisionne (par exemple, une application de ressources humaines dans le cloud comme Workday et SuccessFactors).
- **système cible** : référentiel des utilisateurs vers lequel Azure AD approvisionne (par exemple, AD, Azure AD, Office 365 ou d’autres applications SaaS).
- **Processus entrants-changements de poste-sortants** : terme utilisé pour les nouvelles embauches, les transferts et les licenciements avec une application RH cloud comme système de dossiers. Le processus se termine lorsque le service approvisionne correctement les attributs nécessaires sur le système cible.

### <a name="key-benefits"></a>Principaux avantages

Cette fonctionnalité d’approvisionnement informatique axé sur les ressources humaines offre des avantages significatifs pour l’entreprise, comme indiqué ci-dessous :

- **Augmenter la productivité** : vous pouvez désormais automatiser l’attribution de comptes d’utilisateur et de licences Office 365 et fournir un accès aux groupes de clés. L’automatisation des attributions offre aux nouveaux employés un accès immédiat à leurs outils de travail et augmente la productivité.
- **Gérer le risque** : vous pouvez augmenter la sécurité en automatisant les changements, en fonction du statut des employés ou des appartenances aux groupes grâce aux données transmises à partir de l’application RH cloud. L’automatisation des modifications permet de s’assurer que les identités des utilisateurs et l’accès aux applications clés sont automatiquement mis à jour lorsque les utilisateurs sont transférés ou quittent l’organisation.
- **Conformité et gouvernance des adresses** : Azure AD prend en charge les journaux d’audit natifs pour les demandes d’approvisionnement d’utilisateurs effectuées par les applications des systèmes source et cible. L’audit vous permet ainsi d’effectuer le suivi des personnes qui ont accès aux applications à partir d’un seul écran.
- **Gérer les coûts** : l’approvisionnement automatique réduit les coûts tout en évitant l’inefficacité et les erreurs humaines qui sont inhérents à l’approvisionnement manuel. Cela réduit le besoin de solutions d’approvisionnement d’utilisateurs personnalisées créées au fil du temps à l’aide de plateformes héritées et obsolètes.

### <a name="licensing"></a>Licence

Pour configurer l’application RH cloud à l’intégration de l’approvisionnement d’utilisateurs Azure AD, vous devez disposer d’une [licence Premium Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) valide et d’une licence pour l’application RH cloud telle que Workday ou SuccessFactors.

Vous avez également besoin d’une licence valide à un abonnement Azure AD Premium P1 ou supérieur pour chaque utilisateur provenant de l’application RH cloud et approvisionné dans AD ou Azure AD. Tout nombre incorrect de licences détenues dans l’application RH cloud peut entraîner des erreurs pendant l’approvisionnement d’utilisateurs.

### <a name="prerequisites"></a>Conditions préalables requises

- Accès administrateur général Azure AD pour configurer l’agent d’approvisionnement Azure AD Connect.
- Une instance de test et de production de l’application RH cloud.
- Autorisations d’administrateur dans l’application RH cloud pour créer un utilisateur de l’intégration système et apporter des modifications afin de tester les informations de l’employé.
- Pour l’approvisionnement d’utilisateurs vers AD, un serveur exécutant Windows Server 2012 ou version ultérieure et doté d’un runtime .NET 4.7.1+ est nécessaire afin d’héberger l’[agent d’approvisionnement Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) pour la synchronisation des utilisateurs entre AD et Azure AD.

### <a name="training-resources"></a>Ressources de formation

| **Ressources** | **Lien et description** |
|:-|:-|
| Videos | [Présentation de l’attribution d’utilisateurs dans Azure Active Directory](https://youtu.be/_ZjARPpI6NI) |
| | [Comment déployer l’attribution d’utilisateurs dans Azure Active Directory](https://youtu.be/pKzyts6kfrw) |
| Tutoriels | Consultez la [liste de tutoriels sur l’intégration d’applications SaaS à Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Tutoriel : Configurer Workday pour le provisionnement automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| Questions fréquentes (FAQ) | [Approvisionnement automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Approvisionnement de Workday vers Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architecture de solution

L’exemple suivant décrit l’architecture de la solution d’approvisionnement d’utilisateurs de bout en bout pour les environnements hybrides courants et comprend :

- **Flux de données RH autorisé, de l’application de ressources humaines cloud vers AD** : dans ce flux, l’événement RH (processus entrants-changements de poste-sortants) est initié dans le locataire de l’application RH cloud. Le service d’approvisionnement Azure AD et l’agent d’approvisionnement Azure AD Connect configurent les données utilisateur du locataire de l’application RH cloud vers AD. Selon l’événement, cela peut provoquer des opérations de création/mise à jour/activation/désactivation dans AD.
- **Synchronisation de données avec Azure AD et mise à jour de l’e-mail et du nom d’utilisateur à partir d’AD en local vers l’application RH cloud** : une fois que les comptes sont mis à jour dans AD, ils sont synchronisés avec Azure AD par le biais d’Azure AD Connect, et les adresses e-mail et les attributs du nom d’utilisateur peuvent être mis à jour dans le locataire de l’application RH cloud.

![Diagramme du workflow](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Description du workflow

Les étapes clés indiquées dans le diagramme sont les suivantes :  

1. **L’équipe RH** effectue les transactions dans le locataire de l’application RH cloud.
2. **Le service d’approvisionnement Azure AD** exécute les cycles planifiés à partir du locataire de l’application RH cloud et identifie les modifications qui doivent être traitées pour la synchronisation avec AD.
3. **Le service d’approvisionnement Azure AD** appelle l’agent d’approvisionnement Azure AD Connect avec une charge utile de demandes contenant des opérations de création/de mise à jour/d’activation/de désactivation de compte AD.
4. **L’agent d’approvisionnement Azure AD Connect** utilise un compte de service pour gérer des données de compte AD.
5. **Azure AD Connect** exécute la [synchronisation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) différentielle pour extraire les mises à jour dans AD.
6. Les mises à jour **AD** sont synchronisées avec Azure AD.
7. **Le service d’approvisionnement Azure AD** met à jour l’attribut d’e-mail et le nom d’utilisateur à partir d’Azure AD vers le locataire de l’application RH cloud.

## <a name="plan-the-deployment-project"></a>Planifier le projet de déploiement

Tenez compte des besoins de votre organisation lorsque vous déterminez la stratégie de ce déploiement dans votre environnement.

### <a name="engage-the-right-stakeholders"></a>Impliquer les parties prenantes appropriées

Lorsque des projets technologiques échouent, cela est généralement dû à des attentes qui ne correspondent pas à l’impact, aux résultats et aux responsabilités réels. Pour éviter ces pièges, [veillez à impliquer les parties prenantes appropriées](https://aka.ms/deploymentplans) et à ce qu’elles comprennent bien leurs rôles dans le projet. Pour ce faire, dressez une liste de leurs contributions et de leurs responsabilités.

Vous devez inclure un représentant de l’organisation RH qui peut fournir des informations sur les processus opérationnels RH existants et les exigences en matière de traitement des données relatives à l’identité du travailleur et à ses tâches.

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Vous devez communiquer de manière proactive avec vos utilisateurs sur ce qui va changer, à quel moment les changements seront appliqués et comment ils peuvent obtenir de l’aide en cas de problème.

### <a name="plan-a-pilot"></a>Prévoir un pilote

L’intégration des processus opérationnels RH et des workflows d’identité de l’application RH cloud vers les systèmes cibles requiert une quantité considérable de validation, transformation et nettoyage des données et de test de bout en bout avant de déployer la solution en production.

Nous vous recommandons d’exécuter la configuration initiale dans un [environnement pilote](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) avant de la mettre à l’échelle de tous les utilisateurs en production.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Sélectionner les applications du connecteur d’approvisionnement RH cloud

Pour faciliter les workflows d’approvisionnement Azure AD entre l’application RH cloud et AD, Azure AD, vous pouvez ajouter plusieurs applications du connecteur d’approvisionnement à partir de la galerie d’applications Azure AD :

- **Approvisionnement d’utilisateurs de l’application RH cloud vers AD** : cette application du connecteur d’approvisionnement facilite l’approvisionnement des comptes d’utilisateur à partir de l’application RH cloud vers un domaine AD unique. Si vous avez de plusieurs domaines, vous pouvez ajouter une instance de cette application à partir de la galerie d’applications Azure AD pour chaque domaine AD vers lequel vous devez approvisionner.
- **Approvisionnement d’utilisateurs de l’application RH cloud vers Azure AD** : Azure AD Connect est l’outil à utiliser pour synchroniser les utilisateurs AD avec Azure AD, mais cette application du connecteur d’approvisionnement peut aussi faciliter l’approvisionnement d’utilisateurs « cloud only » de l’application RH cloud vers un locataire Azure AD unique.
- **Mise à jour de l’application RH cloud** : cette application du connecteur d’approvisionnement facilite la mise à jour des adresses e-mail de l’utilisateur d’Azure AD vers l’application RH cloud.

Par exemple, l’image ci-dessous répertorie les applications du connecteur Workday disponibles dans la galerie d’applications Azure AD.

![Galerie d’applications du portail Azure Active Directory](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Organigramme décisionnel

Utilisez l’organigramme décisionnel ci-dessous pour identifier les applications d’approvisionnement RH cloud pertinentes pour votre scénario.

![Organigramme décisionnel](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Concevoir la topologie de déploiement de l’agent d’approvisionnement Azure AD Connect

L’intégration de l’approvisionnement entre l’application de ressources humaines cloud et AD requiert ces quatre composants :

- Locataire de l’application RH cloud
- Application du connecteur d’approvisionnement
- Agent d’approvisionnement Azure AD Connect
- Domaine AD

La topologie de déploiement de l’agent d’approvisionnement Azure AD Connect dépend du nombre de locataires de l’application RH cloud et des sous-domaines AD que vous prévoyez d’intégrer. Si vous avez plusieurs domaines AD, cela dépend si les domaines AD sont contigus ou [disjoint](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

En fonction de votre décision, choisissez l’un des scénarios de déploiement suivants :

- Locataire unique de l’application RH cloud -> ciblez un ou plusieurs sous-domaines AD dans une forêt approuvée
- Locataire unique de l’application RH cloud -> ciblez plusieurs sous-domaines dans une forêt AD disjointe

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Locataire unique de l’application RH cloud -> un ou plusieurs sous-domaines AD cible dans une forêt approuvée

Nous vous recommandons la configuration de production suivante :

|Condition requise|Recommandation|
|:-|:-|
|Nombre d’agents d’approvisionnement Azure AD Connect à déployer|2 (pour la haute disponibilité et le basculement)
|Nombre d’applications du connecteur d’approvisionnement à configurer|Une application par sous-domaine|
|Serveur hôte pour l’agent d’approvisionnement Azure AD Connect|Windows 2012 R2+ avec ligne de vue sur les contrôleurs de domaine AD géo-localisés</br>Peut coexister avec le service Azure AD Connect|

![Flux vers les agents locaux](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Locataire unique de l’application RH cloud -> ciblez plusieurs sous-domaines dans une forêt AD disjointe

Ce scénario implique l’approvisionnement d’utilisateurs de l’application RH cloud vers des domaines situés dans des forêts AD disjointes.

Nous vous recommandons la configuration de production suivante :

|Condition requise|Recommandation|
|:-|:-|
|Nombre d’agents d’approvisionnement Azure AD Connect à déployer localement|2 par forêt AD disjointe|
|Nombre d’applications du connecteur d’approvisionnement à configurer|Une application par sous-domaine|
|Serveur hôte pour l’agent d’approvisionnement Azure AD Connect|Windows 2012 R2+ avec ligne de vue sur les contrôleurs de domaine AD géo-localisés</br>Peut coexister avec le service Azure AD Connect|

![Une seule forêt AD disjointe du locataire de l’application RH cloud](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Configuration requise pour l’agent d’approvisionnement Azure AD Connect

La solution d’approvisionnement d’utilisateurs de l’application RH cloud vers AD nécessite le déploiement d’un ou plusieurs agents d’approvisionnement Azure AD Connect sur des serveurs exécutant Windows 2012 R2 (ou versions ultérieures) avec au moins 4 Go de RAM et un runtime .NET 4.7.1+. Vérifiez que le serveur hôte a un accès réseau au domaine AD cible.

Pour préparer l’environnement local, l’Assistant de configuration de l’agent d’approvisionnement Azure AD Connect inscrit l’agent auprès de votre locataire Azure AD, [ouvre les ports](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [autorise l’accès aux URL](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls) et prend en charge [la configuration du proxy HTTPS sortant](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

L’agent d’approvisionnement utilise un compte de service pour communiquer avec le ou les domaines AD. Avant l’installation de l’agent, nous vous recommandons de créer un compte de service dans les utilisateurs et ordinateurs AD qui répondent aux exigences suivantes :

- Un mot de passe qui n’expire pas
- Des autorisations de contrôle délégué pour lire, créer, supprimer et gérer des comptes d’utilisateur

Vous pouvez sélectionner des contrôleurs de domaine qui doivent gérer les demandes d’approvisionnement. Si vous avez plusieurs contrôleurs de domaine géographiquement répartis, installez l’agent d’approvisionnement dans le même site que vos contrôleurs de domaine par défaut, de façon à améliorer la fiabilité et les performances de la solution de bout en bout.

Pour la haute disponibilité, vous pouvez déployer plusieurs agents d’approvisionnement Azure AD Connect et les inscrire pour gérer le même ensemble de domaines AD locaux.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planifier les filtres d’étendue et le mappage des attributs

Lorsque vous activez l’approvisionnement à partir de l’application RH cloud vers AD ou Azure AD, le Portail Azure contrôle les valeurs d’attribut par le biais du mappage d’attributs.

### <a name="define-scoping-filters"></a>Définir des filtres d’étendue

Utilisez des [filtres d’étendue](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) pour définir les règles basées sur les attributs qui déterminent les utilisateurs qui doivent être approvisionnés à partir de l’application de ressources humaines cloud vers AD ou Azure AD.

Lorsque vous lancez le processus Entrants, rassemblez les spécifications suivantes :

- L’application RH cloud est-elle utilisée pour intégrer à la fois les employés et les travailleurs occasionnels ?
- Envisagez-vous d’utiliser l’approvisionnement d’utilisateurs de l’application de ressources humaines cloud vers Azure AD pour gérer à la fois les employés et les travailleurs occasionnels ?
- Prévoyez-vous de déployer l’approvisionnement d’utilisateurs de l’application RH cloud vers Azure AD uniquement pour un sous-ensemble d’utilisateurs de l’application de ressources humaines cloud (par exemple, les employés uniquement) ?

En fonction de vos besoins, lorsque vous configurez des mappages d’attributs, vous pouvez définir le champ **Étendue de l’objet source** pour sélectionner les ensembles d’utilisateurs de l’application RH cloud qui doivent être dans l’étendue de l’approvisionnement vers AD. Consultez le tutoriel sur les applications de ressources humaines cloud pour obtenir les filtres d’étendue couramment utilisés.

### <a name="determine-matching-attributes"></a>Déterminer le ou les attributs correspondants

Grâce à l’approvisionnement, vous avez la possibilité de mettre en correspondance les comptes existants entre le système source et le système cible. Lorsque vous intégrez l’application RH cloud au service d’approvisionnement Azure AD, vous pouvez [configurer le mappage d’attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) pour déterminer quelles données utilisateur doivent transiter de l’application RH cloud vers AD ou Azure AD.

Lorsque vous lancez le processus Entrants, rassemblez les spécifications suivantes :

- Quel est l’ID unique de cette application RH cloud qui est utilisé pour identifier chaque utilisateur ?
- Du point de vue du cycle de vie des identités, comment gérez-vous les employés réembauchés ? Les employés réembauchés conservent-ils leur ancien ID d’employé ?
- Traitez-vous les embauches futures et créez-vous pour eux des comptes AD à l’avance ?
- Du point de vue du cycle de vie des identités, comment gérez-vous la conversion Employé à Travailleur occasionnel ou autre ?
- Les utilisateurs convertis conservent-ils leur ancien compte AD ou en obtiennent-ils de nouveaux ?

En fonction de vos besoins, Azure AD le prend en charge par mappage direct d’attribut à attribut, en fournissant des valeurs constantes ou [en écrivant des expressions pour les mappages d’attributs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Cette flexibilité vous permet de contrôler très précisément ce qui sera renseigné dans l’attribut de l’application ciblée. Vous pouvez utiliser l’[API Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) et l’Afficheur Graph pour exporter vos mappages d’attributs et votre schéma d’attribution d’utilisateurs dans un fichier JSON, et le réimporter dans Azure AD.

**Par défaut**, l’attribut de l’application RH cloud qui représente l’ID d’employé unique est utilisé comme attribut correspondant *mappé à l’attribut unique dans AD.* Par exemple, dans le scénario de l’application Workday, l’attribut *WorkerID* de *Workday* est mappé à l’attribut *employeeID* d’AD.

Vous pouvez définir plusieurs attributs de correspondance et attribuer la priorité de correspondance. Ils sont évalués sur la priorité de correspondance. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.

Vous pouvez également [personnaliser les mappages d’attributs par défaut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) pour notamment modifier ou supprimer des mappages d’attributs existants ou créer des mappages d’attributs en fonction des besoins de votre entreprise. Pour obtenir la liste des attributs personnalisés à mapper, consultez le tutoriel sur l’application de ressources humaines cloud (par exemple, [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)).

### <a name="determine-user-account-status"></a>Déterminer l’état du compte d’utilisateur

Par défaut, l’application du connecteur d’approvisionnement mappe l’**état du profil utilisateur RH** à l’**état du compte d’utilisateur** dans AD/Azure AD pour déterminer s’il faut activer ou désactiver le compte d’utilisateur.

Lorsque vous lancez le processus Entrants/Sortants, rassemblez les spécifications suivantes :

| Process | Spécifications |
| - | - |
| **Entrants** | Du point de vue du cycle de vie des identités, comment gérez-vous les employés réembauchés ? Les employés réembauchés conservent-ils leur ancien ID d’employé ? |
| | Traitez-vous les embauches futures et créez-vous pour eux des comptes AD à l’avance ? Ces comptes sont-ils créés dans un état activé/désactivé ? |
| | Du point de vue du cycle de vie des identités, comment gérez-vous la conversion Employé à Travailleur occasionnel ou autre ? |
| | Les utilisateurs convertis conservent-ils leur ancien compte AD ou en obtiennent-ils de nouveaux ? |
| **Sortants** | Les licenciements sont-ils gérés différemment pour les employés et les travailleurs occasionnels dans AD ? |
| | Quelles sont les dates d’effet prises en compte pour le traitement du licenciement des utilisateurs ? |
| | Quel est l’impact des conversions des employés et des travailleurs occasionnels sur les comptes AD existants ? |
| | Comment traitez-vous l’opération « Annuler » dans AD ? Les opérations d’annulation doivent être gérées si des embauches futures sont créées dans AD dans le cadre du processus Entrants. |

En fonction de vos besoins, vous pouvez personnaliser la logique de mappage à l’aide d’[expressions Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) afin que le compte AD soit activé ou désactivé selon une combinaison de points de données.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mapper les attributs d’utilisateur de l’application RH cloud vers AD

Chaque application de ressources humaines cloud est fournie avec des mappages par défaut de l’application de ressources humaines cloud vers AD.

Lorsque vous lancez le processus Entrants/Changements de poste/Sortants, rassemblez les spécifications suivantes :

| Process | Spécifications |
| - | - |
| **Entrants** | Le processus de création de compte AD est-il manuel, automatisé ou partiellement automatisé ? |
| | Envisagez-vous de propager des attributs personnalisés de l’application RH cloud vers AD ? |
| **Changements de poste** | Quels attributs voulez-vous traiter quand une opération « Changements de poste » a lieu dans l’application RH cloud ? |
| | Effectuez-vous des validations d’attributs spécifiques au moment des mises à jour de l’utilisateur ? Si oui, veuillez préciser. |
| **Sortants** | Les licenciements sont-ils gérés différemment pour les employés et les travailleurs occasionnels dans AD ? |
| | Quelles sont les dates d’effet prises en compte pour le traitement du licenciement des utilisateurs ? |
| | Quel est l’impact des conversions des employés et des travailleurs occasionnels sur les comptes AD existants ? |

En fonction de vos besoins, vous pouvez modifier les mappages pour répondre à vos objectifs d’intégration. Pour obtenir la liste des attributs personnalisés à mapper, consultez le tutoriel sur l’application de ressources humaines cloud spécifique (par exemple, [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)).

### <a name="generate-unique-attribute-value"></a>Générer une valeur d’attribut unique

Lorsque vous lancez le processus Entrants, vous devrez peut-être générer des valeurs d’attribut uniques lorsque vous définissez des attributs tels que CN, samAccountName et UPN qui ont des contraintes uniques.

La fonction [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) d’Azure AD évalue chaque règle, puis vérifie la valeur générée pour l’unicité dans le système cible. Voir l’exemple [Générer une valeur unique pour l’attribut userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Cette fonction est actuellement prise en charge uniquement pour l’« Approvisionnement d’utilisateurs de Workday vers Active Directory ». Elle ne peut pas être utilisée avec d’autres applications d’approvisionnement.

### <a name="configure-ad-ou-container-assignment"></a>Configurer l’attribution de conteneur à des unités d’organisation AD

Il est souvent nécessaire de placer les comptes d’utilisateur AD dans des conteneurs basés sur des unités commerciales, des emplacements et des services. Lorsque vous lancez un processus Changements de poste et que l’organisme de supervision change, vous devrez peut-être déplacer l’utilisateur d’une unité d’organisation vers une autre dans AD.

Utilisez la fonction [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) pour configurer la logique métier de l’attribution de l’unité d’organisation et la mapper à l’attribut AD *parentDistinguishedName*.

Par exemple, si vous souhaitez créer des utilisateurs dans l’unité d’organisation en fonction de l’attribut RH « Municipalité », vous pouvez utiliser l’expression suivante.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Avec cette expression, si la valeur Municipalité est Dallas, Austin, Seattle ou Londres, le compte d’utilisateur est créé dans l’unité d’organisation correspondante. Si aucune correspondance n’est trouvée, le compte est créé dans l’unité d’organisation par défaut.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planifier la remise de mot de passe pour de nouveaux comptes d’utilisateur

Lorsque vous lancez le processus Entrants, vous devez définir et remettre un mot de passe temporaire pour les nouveaux comptes d’utilisateur. Grâce à l’approvisionnement d’utilisateurs de l’application RH cloud vers Azure AD, vous pouvez déployer la fonction de [réinitialisation de mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) Azure AD pour l’utilisateur dès le premier jour.

La SSPR est un moyen simple pour les administrateurs informatiques de permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes. Vous pouvez approvisionner l’attribut **Numéro de téléphone** de l’application RH cloud vers AD, puis le synchroniser avec Azure AD. Une fois que le **Numéro de téléphone mobile** se trouve dans Azure AD, vous pouvez activer la SSPR sur le compte de l’utilisateur. Ainsi, le jour de sa prise de fonction, les nouveaux utilisateurs pourront utiliser le numéro enregistré et vérifié pour s’authentifier.

## <a name="plan-for-initial-cycle"></a>Planifier le cycle initial

Lorsque le service d’approvisionnement Azure AD s’exécute pour la première fois, il effectue un [cycle initial](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle) sur l’application RH cloud pour créer un instantané de tous les objets utilisateur se trouvant dans l’application RH cloud. Le temps nécessaire pour les cycles initiaux dépend directement du nombre d’utilisateurs présents dans le système source. Le cycle initial pour certains locataires de l’application RH cloud ayant plus de 100 000 utilisateurs peut prendre beaucoup de temps.

**Pour les locataires de l’application RH cloud volumineux (> 30 000 utilisateurs), nous vous recommandons** d’exécuter le cycle initial en étapes progressives et de lancer les mises à jour incrémentielles uniquement après avoir vérifié que les attributs appropriés sont définis dans AD pour différents scénarios d’approvisionnement d’utilisateurs. Suivez l’ordre ci-dessous :

1. Exécutez le cycle initial uniquement pour un ensemble limité d’utilisateurs en paramétrant le [filtre d’étendue](#plan-scoping-filters-and-attribute-mapping).
2. Vérifiez que les valeurs d’attribut et d’approvisionnement du compte AD sont définies pour les utilisateurs sélectionnés pour la première exécution. Si le résultat répond à vos attentes, développez le filtre d’étendue pour inclure progressivement plus d’utilisateurs et vérifier les résultats de la deuxième exécution.

Une fois que vous êtes satisfait des résultats du cycle initial pour les utilisateurs de test, vous pouvez lancer les [mises à jour incrémentielles](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planifier les tests et la sécurité

À chaque étape de votre déploiement, du pilote initial à l’activation de l’approvisionnement d’utilisateurs, assurez-vous que les tests que vous effectuez donnent les résultats attendus et que vous auditez les cycles d’approvisionnement.

### <a name="plan-testing"></a>Planifier les tests

Une fois que vous avez configuré l’approvisionnement d’utilisateurs de l’application RH cloud vers Azure AD, vous devez exécuter des cas de test pour vérifier que cette solution répond aux besoins de votre organisation.

|Scénarios|Résultats attendus|
|:-|:-|
|Embauche d’un nouvel employé dans l’application RH cloud| - Le compte d’utilisateur est approvisionné dans AD.</br>- Les utilisateurs peuvent se connecter à des applications de domaine AD et effectuer les actions souhaitées.</br>- Si la synchronisation AAD Connect est configurée, le compte d’utilisateur est également créé dans Azure AD.
|L’utilisateur est licencié dans l’application RH cloud|- Le compte d’utilisateur est désactivé dans AD.</br>- L’utilisateur ne peut pas se connecter aux applications de l’entreprise qui sont protégées par AD.
|L’organisme de supervision des utilisateurs est mise à jour dans l’application RH cloud|En fonction du mappage des attributs, le compte d’utilisateur passe d’une unité d’organisation à une autre dans AD.|
|Les RH mettent à jour le responsable de l’utilisateur dans l’application RH cloud|Le champ Responsable dans AD est mis à jour pour refléter le nom du nouveau responsable.|
|Les RH réembauchent un employé à un nouveau poste.|Le comportement dépend de la façon dont l’application RH cloud est configurée pour générer des ID d’employés :</br>- Si l’ancien ID d’employé est réutilisé pour les employés réembauchés, le connecteur activera le compte AD existant pour l’utilisateur.</br>- Si les employés réembauchés obtiennent un nouvel ID d’employé, le connecteur créera un nouveau compte AD pour l’utilisateur.|
|Les RH convertissent l’employé en travailleur occasionnel ou vice versa|Un nouveau compte AD est créé pour le nouveau personnage et l’ancien compte est désactivé à la date d’effet de la conversion.|

Utilisez les résultats ci-dessus pour déterminer comment faire passer votre implémentation automatique de l’approvisionnement d’utilisateurs en production en fonction de vos délais établis.

> [!TIP]
> Nous vous recommandons d’utiliser des techniques telles que la réduction des données et le nettoyage des données lors de l’actualisation de l’environnement de test à l’aide de données de production pour supprimer ou masquer les données sensibles relatives aux informations d’identification personnelle pour respecter les normes de sécurité et de confidentialité.

### <a name="plan-security"></a>Planifier la sécurité

Il est fréquent qu’une révision de sécurité soit nécessaire dans le cadre du déploiement d’un nouveau service. Si une révision de sécurité est nécessaire ou n’a pas encore été effectuée, consultez les nombreux [livres blancs](https://www.microsoft.com/download/details.aspx?id=36391) Azure AD qui fournissent une vue d’ensemble de l’identité en tant que service.

### <a name="plan-rollback"></a>Planifier la restauration

Si l’implémentation de l’approvisionnement d’utilisateurs dans l’application RH cloud ne fonctionne pas comme vous le souhaitez dans l’environnement de production, les étapes de restauration ci-dessous peuvent vous aider à revenir à un état précédent connu pour être correct :

1. Consultez le [rapport de synthèse sur l’approvisionnement](check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) et les [journaux d’approvisionnement](check-status-user-account-provisioning.md#provisioning-logs-preview) (voir [Gérer l’approvisionnement d’utilisateurs pour l’application RH cloud](#manage-your-configuration)) pour déterminer les opérations incorrectes qui ont été effectuées sur les utilisateurs et/ou les groupes concernés.
2. Le dernier état correct connu des utilisateurs et/ou des groupes concernés peut être déterminé par l’intermédiaire des journaux d’audit de l’approvisionnement ou en examinant les systèmes cibles (Azure AD ou AD).
3. Collaborez avec le propriétaire de l’application pour mettre à jour les utilisateurs et/ou groupes concernés directement dans l’application, à l’aide des dernières valeurs d’état connues pour être correctes.

## <a name="deploy-the-cloud-hr-app"></a>Déployer l’application RH cloud

Choisissez l’application RH cloud qui correspond aux exigences de votre solution.

**Workday** : Consultez [Tutoriel : Configurer Workday pour l’approvisionnement automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) pour connaître les étapes à suivre pour importer des profils d’employé dans AD et Azure AD à partir de Workday, avec mise à jour facultative de l’adresse e-mail et du nom d’utilisateur dans Workday.

## <a name="manage-your-configuration"></a>Gérer votre configuration

Azure AD peut fournir des insights supplémentaires sur l’attribution d’utilisateurs et l’intégrité opérationnelle via les journaux d’audit et les rapports.

### <a name="gain-insights-from-reports-and-logs"></a>Obtenir des informations à partir des rapports et des journaux

Après l’exécution d’un [cycle initial](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle) réussi, le service d’approvisionnement Azure AD continue d’exécuter indéfiniment des mises à jour incrémentielles dos à dos, à des intervalles définis dans les tutoriels propres à chaque application, jusqu’à ce que l’un des événements suivants se produise :

- Le service est arrêté manuellement, et un nouveau cycle initial est déclenché à l’aide du [Portail Azure](https://portal.azure.com/) ou de la commande [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriée.
- Un nouveau cycle initial est déclenché en raison d’une modification dans les mappages d’attributs ou les filtres d’étendue.
- Le processus d’approvisionnement est mis en quarantaine en raison d’un taux d’erreur élevé, et reste en quarantaine pendant plus de quatre semaines à l’issue desquelles il sera automatiquement désactivé.

Pour passer en revue ces événements, et toutes les autres activités effectuées par le service d’approvisionnement, [découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor

Toutes les activités effectuées par le service d’approvisionnement sont enregistrées dans les **journaux d’audit Azure AD**. Vous pouvez acheminer les journaux d’audit Azure AD vers des journaux Azure Monitor pour une analyse plus poussée. Les **journaux d’activité Azure Monitor (également appelés espace de travail Log Analytics)** vous permettent d’interroger les données pour trouver des événements, analyser les tendances et effectuer une corrélation entre différentes sources de données. Regardez cette [vidéo](https://youtu.be/MP5IaCTwkQg) pour découvrir les avantages de l’utilisation de journaux d’activité Azure Monitor pour Azure AD dans des scénarios utilisateur pratique.

Vous pouvez installer les [affichages Log Analytics pour les journaux d’activité Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views). Vous avez ainsi accès à différents [rapports prédéfinis](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) qui affichent des événements d’approvisionnement qui se produisent dans votre environnement.

Pour plus d’informations, consultez [Comment analyser les journaux d’activité Azure AD avec vos journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Gérer des données personnelles

L’agent d’approvisionnement Azure AD Connect installé sur le serveur Windows crée des journaux dans le journal des événements Windows qui peuvent contenir des données personnelles selon vos mappages d’attributs de l’application de ressources humaines cloud vers AD. Pour respecter les obligations de confidentialité de l’utilisateur, vous devez configurer une tâche planifiée Windows pour effacer le journal des événements et veiller à ce qu’aucune donnée ne soit conservée plus de 48 heures.

Le service d’approvisionnement Azure AD Connect ne génère pas de rapports, n’effectue pas d’analyses et ne fournit pas d’insights au-delà de 30 jours. Par conséquent, le service d'approvisionnement Azure AD ne stocke pas, ne traite pas et ne conserve pas de données au-delà de 30 jours. 

### <a name="troubleshoot"></a>Dépanner

Reportez-vous aux liens suivants pour résoudre les problèmes susceptibles de survenir au cours du provisionnement :

- [Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Synchroniser un attribut entre votre instance Active Directory local et Azure AD pour le provisionnement d’une application](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [L’attribution d’utilisateurs dans une application de la galerie Azure AD prend des heures](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Aucun utilisateur n’est attribué à une application de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Un mauvais ensemble d’utilisateurs est attribué à une application de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Configuration de l'observateur d'événements Windows pour résoudre les problèmes liés à l'agent](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configuration des journaux d’activité d’audit du portail Azure pour résoudre les problèmes de service](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Familiarisation avec les journaux d’activité pour les opérations de création de compte d’utilisateur AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Familiarisation avec les journaux d’activité pour les opérations de mise à jour de l’attribut manager](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Résolution des erreurs les plus fréquentes](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Étapes suivantes

- [Écriture d’expressions pour les mappages d’attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)
- [Ignorer la suppression des comptes d’utilisateurs qui sortent de l’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Agent de provisionnement Azure AD Connect : Historique de publication des versions](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
