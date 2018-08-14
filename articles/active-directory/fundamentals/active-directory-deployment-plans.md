---
title: Plans de déploiement Azure Active Directory | Microsoft Docs
description: Fournit des conseils de bout en bout sur le déploiement des fonctionnalités Azure Active Directory
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: 07d3915fd007c0827b885b0603eb176b9e408576
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39508360"
---
# <a name="azure-active-directory-deployment-plans"></a>Plans de déploiement Azure Active Directory
Vous souhaitez des conseils de bout en bout sur la façon de déployer certaines fonctionnalités Azure Active Directory (Azure AD) ? Les plans de déploiement suivants aborderont les notions de base (valeur commerciale, planification, conception et procédures opérationnelles) nécessaires pour réussir le lancement de quelques-unes des fonctionnalités Azure AD les plus courantes. 

Vous y trouverez des modèles d’e-mail, des diagrammes d’architecture système, des cas de test et bien plus encore. 

Nous aimerions avoir votre avis sur ces documents. Aussi, nous vous invitons à répondre à cette courte [enquête](https://aka.ms/deploymentplanfeedback) sur la façon dont ces documents ont répondu à vos attentes. 

|Scénario |Description |
|-|-|
|[Authentification unique](https://aka.ms/SSODPDownload)|Avec l’authentification unique, vous pouvez accéder à toutes les applications et ressources dont vous avez besoin pour travailler, et ce, en vous connectant une seule fois avec un seul compte d’utilisateur. Une fois connecté, vous pouvez basculer de Microsoft Office à SalesForce ou Box sans devoir vous authentifier à nouveau (par exemple, en tapant un mot de passe).|
|[Approvisionnement d’utilisateurs](https://aka.ms/UserProvisioningDPDownload)|Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud (SaaS) comme Dropbox, Salesforce, ServiceNow, etc.|
|[Azure Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. À l’aide des méthodes d’authentification approuvées par l’administrateur, Azure MFA contribue à sécuriser l’accès aux données et applications, tout en répondant à la nécessité de mettre en place un processus d’authentification simple.|
|[Accès conditionnel](https://aka.ms/CADPDownload)|Avec l’accès conditionnel, vous pouvez implémenter des décisions de contrôle d’accès automatisées pour accéder à vos applications cloud qui sont basées sur des conditions.|
|[ADFS et authentification directe](https://aka.ms/ADFSTOPTADPDownload)|L’authentification directe Azure AD permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Cette fonctionnalité offre aux utilisateurs une meilleure expérience : moins de mots de passe à mémoriser et réduction des coûts de support technique informatique, car les utilisateurs sont moins susceptibles d’oublier comment se connecter. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide leurs mots de passe directement à partir de l’installation Active Directory locale.|
|[ADFS et synchronisation de hachage de mot de passe](https://aka.ms/ADFSTOPHSDPDownload)|Avec la synchronisation de hachage de mot de passe, les hachages des mots de passe utilisateur sont synchronisés sur Azure AD à partir de l’installation Active Directory locale. Ainsi, Azure AD permet d’authentifier les utilisateurs sans aucune interaction avec l’installation Active Directory locale.|
|[Authentification unique fluide](https://aka.ms/SeamlessSSODPDownload)|L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, de taper leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires.|
|[Réinitialisation de mot de passe en libre service](https://aka.ms/SSPRDPDownload)|La réinitialisation de mot de passe en libre-service offre à vos utilisateurs la possibilité de réinitialiser leur mot de passe, sans intervention de l’administrateur, quand et où ils en ont besoin.|
|[Proxy d’application Azure AD](https://aka.ms/AppProxyDPDownload)|Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils veulent pouvoir travailler sur leurs propres appareils (tablettes, téléphones ou ordinateurs portables). Ils veulent aussi pouvoir accéder à toutes leurs applications, qu’il s’agisse d’applications SaaS dans le cloud ou d’applications métier installées en local. La fourniture d’un accès aux applications locales implique généralement le recours à des réseaux privés virtuels (VPN) ou à des zones démilitarisées (DMZ). Non seulement ces solutions sont complexes et difficiles à sécuriser, mais leur configuration et leur gestion ont un coût élevé. Il y a une meilleure solution. - Proxy d’application Azure AD|
