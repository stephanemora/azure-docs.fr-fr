---
title: Plans de déploiement - Azure Active Directory | Microsoft Docs
description: Conseils de bout en bout sur le déploiement d’un grand nombre de fonctionnalités Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa7c4865313a48ae5d9041d2dceb6c91787bb6be
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733547"
---
# <a name="azure-active-directory-deployment-plans"></a>Plans de déploiement Azure Active Directory
Vous souhaitez des conseils de bout en bout sur la façon de déployer certaines fonctionnalités Azure Active Directory (Azure AD) ? Les plans de déploiement suivants aborderont les notions de base (valeur commerciale, planification, conception et procédures opérationnelles) nécessaires pour réussir le lancement de quelques-unes des fonctionnalités Azure AD les plus courantes. 

Vous y trouverez des modèles d’e-mail, des diagrammes d’architecture système, des cas de test et bien plus encore. 

Nous aimerions avoir votre avis sur ces documents. Aussi, nous vous invitons à répondre à cette courte [enquête](https://aka.ms/deploymentplanfeedback) sur la façon dont ces documents ont répondu à vos attentes. 

## <a name="include-the-right-stakeholders"></a>Inclure les parties prenantes droite

Lorsque vous commencez votre planification du déploiement pour une nouvelle fonctionnalité, il est important d’inclure des principales parties prenantes au sein de votre organisation. Nous vous recommandons d’identifier et de documenter l’ou les personnes pour répondre à chacun des rôles suivants et les utiliser pour déterminer leur implication dans le projet.  

Les rôles peuvent inclure les éléments suivants 

|Rôle |Description |
|-|-|
|Utilisateur final|Un groupe représentatif d’utilisateurs pour lesquels la fonctionnalité sera implémentée. Aperçus souvent les modifications dans un programme pilote.
|Responsable du Support informatique|Représentant d’organisation du support informatique qui peut fournir des commentaires sur la prise en charge de cette modification à partir d’un point de vue du support technique.  
|Architecte de l’identité ou Azure administrateur général|Identité gestion responsable chargé définissant comment cette modification est alignée avec l’infrastructure de gestion d’identité core dans votre organisation.|
|Propriétaire de l’entreprise |Le propriétaire de l’activité globale de l’ou les applications affectée, qui peut inclure la gestion de l’accès.  Peut également fournir une entrée sur l’expérience utilisateur et l’utilité de cette modification du point de vue d’un utilisateur final.
|Propriétaire de la sécurité|Un représentant de l’équipe de sécurité qui permettre se déconnecter pour que le plan respectent les exigences de sécurité de votre organisation.|
|Compliance Manager|La personne au sein de votre organisation responsable de la conformité par l’entreprise, du secteur ou exigences gouvernementales.|

**Niveaux d’implication peuvent inclure :**

- **R**esponsible pour l’implémentation du plan de projet et le résultat 

- **Un**pprobation du plan de projet et des résultats 

- **C**ontributor au plan de projet et le résultat 

- **J’ai**nformed du plan de projet et des résultats
 
## <a name="deployment-plans"></a>Plans de déploiement



|Scénario |Description |
|-|-|
|[Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. À l’aide des méthodes d’authentification approuvées par l’administrateur, Azure MFA contribue à sécuriser l’accès aux données et applications, tout en répondant à la nécessité de mettre en place un processus d’authentification simple.|
|[Accès conditionnel](https://aka.ms/deploymentplans/ca)|Avec l’accès conditionnel, vous pouvez implémenter des décisions de contrôle d’accès automatisées pour accéder à vos applications cloud qui sont basées sur des conditions.|
|[Réinitialisation de mot de passe en libre service](https://aka.ms/SSPRDPDownload)|La réinitialisation de mot de passe en libre-service offre à vos utilisateurs la possibilité de réinitialiser leur mot de passe, sans intervention de l’administrateur, quand et où ils en ont besoin.|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Azure AD Privileged Identity Management (PIM) vous aide à gérer les rôles d’administrateur privilégiés dans Azure AD, les ressources Azure et d’autres services Microsoft Online Services. PIM fournit des solutions telles que l’accès juste-à-temps, des workflows d’approbation de demande et des révisions d’accès entièrement intégrées pour que vous puissiez identifier, découvrir et empêcher les activités malveillantes de rôles privilégiés en temps réel.|
|[Authentification unique](https://aka.ms/SSODPDownload)|Avec l’authentification unique, vous pouvez accéder à toutes les applications et ressources dont vous avez besoin pour travailler, et ce, en vous connectant une seule fois avec un seul compte d’utilisateur. Une fois connecté, vous pouvez basculer de Microsoft Office à SalesForce ou Box sans devoir vous authentifier à nouveau (par exemple, en tapant un mot de passe).|
|[Authentification unique fluide](https://aka.ms/SeamlessSSODPDownload)|L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, de taper leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires.|
|[Volet d’accès](https://aka.ms/AccessPanelDPDownload)|Offrez aux utilisateurs un hub simple leur permettant de découvrir toutes leurs applications et d’y accéder. Donnez-leur la possibilité d’accroître leur productivité grâce à des fonctionnalités en libre-service, telles que la possibilité de demander l’accès à de nouveaux groupes et applications ou de gérer l’accès à ces ressources pour le compte d’autres utilisateurs.|
|[ADFS et synchronisation de hachage de mot de passe](https://aka.ms/deploymentplans/adfs2phs)|Avec la synchronisation de hachage de mot de passe, les hachages des mots de passe utilisateur sont synchronisés sur Azure AD à partir de l’installation Active Directory locale. Ainsi, Azure AD permet d’authentifier les utilisateurs sans aucune interaction avec l’installation Active Directory locale.|
|[ADFS et authentification directe](https://aka.ms/deploymentplans/adfs2pta)|L’authentification directe Azure AD permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Cette fonctionnalité offre aux utilisateurs une meilleure expérience : moins de mots de passe à mémoriser et réduction des coûts de support technique informatique, car les utilisateurs sont moins susceptibles d’oublier comment se connecter. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide leurs mots de passe directement à partir de l’installation Active Directory locale.|
|[Proxy d’application Azure AD](https://aka.ms/deploymentplans/appproxy)|Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils veulent pouvoir travailler sur leurs propres appareils (tablettes, téléphones ou ordinateurs portables). Ils veulent aussi pouvoir accéder à toutes leurs applications, qu’il s’agisse d’applications SaaS dans le cloud ou d’applications métier installées en local. La fourniture d’un accès aux applications locales implique généralement le recours à des réseaux privés virtuels (VPN) ou à des zones démilitarisées (DMZ). Non seulement ces solutions sont complexes et difficiles à sécuriser, mais leur configuration et leur gestion ont un coût élevé. Il y a une meilleure solution. - Proxy d’application Azure AD|
|[Approvisionnement d’utilisateurs](https://aka.ms/UserProvisioningDPDownload)|Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud (SaaS) comme Dropbox, Salesforce, ServiceNow, etc.|
|[Provisionnement entrant des utilisateurs piloté par Workday](https://aka.ms/WorkdayDeploymentPlan)|Le provisionnement entrant des utilisateurs vers Active Directory piloté par Workday crée une base pour la gouvernance continue des identités et améliore la qualité des processus métier qui s’appuient sur les données d’identité faisant autorité. À l’aide de cette fonctionnalité, vous pouvez gérer en toute transparence le cycle de vie des identités des employés et des intérimaires en configurant des règles qui mappent les processus JLM (Joiner-Mover-Leaver) (nouveau recrutement, résiliation ou transfert, par exemple) aux actions d’approvisionnement du service informatique (telles que la création, l’activation, la désactivation et la suppression de comptes).|
