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
ms.openlocfilehash: 7f695d9f0240f8c27ea0bedba7e532d37a177752
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304741"
---
# <a name="azure-active-directory-deployment-plans"></a>Plans de déploiement Azure Active Directory
Vous souhaitez des conseils de bout en bout sur la façon de déployer certaines fonctionnalités Azure Active Directory (Azure AD) ? Les plans de déploiement suivants aborderont les notions de base (valeur commerciale, planification, conception et procédures opérationnelles) nécessaires pour réussir le lancement de quelques-unes des fonctionnalités Azure AD les plus courantes. 

Vous y trouverez des modèles d’e-mail, des diagrammes d’architecture système, des cas de test et bien plus encore. 

Nous aimerions avoir votre avis sur ces documents. Aussi, nous vous invitons à répondre à cette courte [enquête](https://aka.ms/deploymentplanfeedback) sur la façon dont ces documents ont répondu à vos attentes. 

## <a name="include-the-right-stakeholders"></a>Inclure les parties prenantes appropriées

Lorsque vous commencez la planification du déploiement d’une nouvelle fonctionnalité, il est important d’inclure les parties prenantes clés au sein de votre organisation. Nous vous recommandons d’identifier et de documenter la ou les personnes qui occupent les rôles suivants et de travailler avec elles pour déterminer leur implication dans le projet.  

Les rôles peuvent inclure : 

|Role |Description |
|-|-|
|Utilisateur final|Groupe représentatif des utilisateurs pour lesquels la fonctionnalité sera implémentée. Visualise souvent la préversion des changements dans un programme pilote.
|Responsable du support informatique|Représentant de l’organisation de support informatique qui peut fournir une entrée sur la capacité de prise en charge de ce changement du point de vue du support technique.  
|Architecte de l’identité ou administrateur général Azure|Représentant de l’équipe de gestion des identités, responsable de la définition de l’alignement de ce changement sur l’infrastructure principale de gestion des identités dans votre organisation.|
|Responsable d’entreprise de l’application |Dans l’entreprise, responsable général de la ou des applications affectées, qui peuvent inclure la gestion de l’accès.  Il peut également fournir une entrée sur l’expérience utilisateur et l’utilité de ce changement du point de vue d’un utilisateur final.
|Responsable de la sécurité|Représentant de l’équipe de sécurité qui peut valider que le plan répondra aux exigences de sécurité de votre organisation.|
|Compliance Manager|Personne responsable au sein de votre organisation de la conformité aux exigences de l’entreprise, du secteur et du gouvernement.|

**Les niveaux d’implication peuvent inclure :**

- **R**esponsable de la mise en œuvre du plan de projet et des résultats 

- **A**pprobation du plan de projet et des résultats 

- **C**ontributeur au plan de projet et aux résultats 

- **I**nformé du plan de projet et des résultats
 
## <a name="deployment-plans"></a>Plans de déploiement



|Scénario |Description |
|-|-|
|[Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. À l’aide des méthodes d’authentification approuvées par l’administrateur, Azure MFA contribue à sécuriser l’accès aux données et applications, tout en répondant à la nécessité de mettre en place un processus d’authentification simple.|
|[Accès conditionnel](https://aka.ms/deploymentplans/ca)|Avec l’accès conditionnel, vous pouvez mettre en œuvre des décisions de contrôle d’accès automatisées pour déterminer qui peut accéder à vos applications cloud, sur la base de conditions.|
|[Réinitialisation de mot de passe en libre service](https://aka.ms/deploymentplans/sspr)|La réinitialisation de mot de passe en libre-service offre à vos utilisateurs la possibilité de réinitialiser leur mot de passe, sans intervention de l’administrateur, quand et où ils en ont besoin.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) vous aide à gérer les rôles d’administrateur privilégiés dans Azure AD, les ressources Azure et d’autres services Microsoft Online Services. PIM fournit des solutions telles que l’accès juste-à-temps, des workflows d’approbation de demande et des révisions d’accès entièrement intégrées pour que vous puissiez identifier, découvrir et empêcher les activités malveillantes de rôles privilégiés en temps réel.|
|[Authentification unique](https://aka.ms/deploymentplans/sso)|Avec l’authentification unique, vous pouvez accéder à toutes les applications et ressources dont vous avez besoin pour travailler, et ce, en vous connectant une seule fois avec un seul compte d’utilisateur. Une fois connecté, vous pouvez basculer de Microsoft Office à SalesForce ou Box sans devoir vous authentifier à nouveau (par exemple, en tapant un mot de passe).|
|[Authentification unique fluide](https://aka.ms/SeamlessSSODPDownload)|L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, de taper leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires.|
|[Volet d’accès](https://aka.ms/AccessPanelDPDownload)|Offrez aux utilisateurs un hub simple leur permettant de découvrir toutes leurs applications et d’y accéder. Donnez-leur la possibilité d’accroître leur productivité grâce à des fonctionnalités en libre-service, telles que la possibilité de demander l’accès à de nouveaux groupes et applications ou de gérer l’accès à ces ressources pour le compte d’autres utilisateurs.|
|[ADFS et synchronisation de hachage de mot de passe](https://aka.ms/deploymentplans/adfs2phs)|Avec la synchronisation de hachage de mot de passe, les hachages des mots de passe utilisateur sont synchronisés sur Azure AD à partir de l’installation Active Directory locale. Ainsi, Azure AD permet d’authentifier les utilisateurs sans aucune interaction avec l’installation Active Directory locale.|
|[ADFS et authentification directe](https://aka.ms/deploymentplans/adfs2pta)|L’authentification directe Azure AD permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Cette fonctionnalité offre aux utilisateurs une meilleure expérience : moins de mots de passe à mémoriser et réduction des coûts de support technique informatique, car les utilisateurs sont moins susceptibles d’oublier comment se connecter. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide leurs mots de passe directement à partir de l’installation Active Directory locale.|
|[Proxy d’application Azure AD](https://aka.ms/deploymentplans/appproxy)|Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils veulent pouvoir travailler sur leurs propres appareils (tablettes, téléphones ou ordinateurs portables). Ils veulent aussi pouvoir accéder à toutes leurs applications, qu’il s’agisse d’applications SaaS dans le cloud ou d’applications métier installées en local. La fourniture d’un accès aux applications locales implique généralement le recours à des réseaux privés virtuels (VPN) ou à des zones démilitarisées (DMZ). Non seulement ces solutions sont complexes et difficiles à sécuriser, mais leur configuration et leur gestion ont un coût élevé. Il y a une meilleure solution. - Proxy d’application Azure AD|
|[Approvisionnement d’utilisateurs](https://aka.ms/UserProvisioningDPDownload)|Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud (SaaS) comme Dropbox, Salesforce, ServiceNow, etc.|
|[Provisionnement entrant des utilisateurs piloté par Workday](https://aka.ms/WorkdayDeploymentPlan)|Le provisionnement entrant des utilisateurs vers Active Directory piloté par Workday crée une base pour la gouvernance continue des identités et améliore la qualité des processus métier qui s’appuient sur les données d’identité faisant autorité. À l’aide de cette fonctionnalité, vous pouvez gérer en toute transparence le cycle de vie des identités des employés et des intérimaires en configurant des règles qui mappent les processus JLM (Joiner-Mover-Leaver) (nouveau recrutement, résiliation ou transfert, par exemple) aux actions d’approvisionnement du service informatique (telles que la création, l’activation, la désactivation et la suppression de comptes).|
