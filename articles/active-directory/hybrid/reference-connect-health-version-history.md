---
title: Historique de publication des versions d’Azure AD Connect Health
description: Ce document décrit les versions d’Azure AD Connect Health et ce qui a été inclus dans ces versions.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 970df215f4a2e7619d43594734b9f6c3476bc609
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46305878"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health : historique de publication des versions
L’équipe Azure Active Directory met régulièrement à jour Azure AD Connect Health avec de nouvelles fonctions et fonctionnalités. Cet article répertorie les versions et les fonctionnalités qui ont été publiées.
Azure AD Connect Health pour la synchronisation est intégré à l’installation d’Azure AD Connect. En savoir plus sur [l’historique de publication d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)


## <a name="august-2018"></a>Août 2018 
*  Agent Azure AD Connect Health pour la synchronisation (version 3.1.7.0) fourni avec Azure AD Connect version 1.1.880.0    
   1. Correctif logiciel pour le [problème de l’agent de surveillance concernant une utilisation intensive de l’UC avec les versions de la Base de connaissances .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juin 2018 
**Nouvelles fonctionnalités préliminaires :** 
* Azure AD Connect Health pour la synchronisation permet de diagnostiquer et de corriger les erreurs de synchronisation d’attribut en double depuis le portail 

**Mise à jour de l’agent :** 
*  Agent Azure AD Connect Health pour AD DS (version 3.1.7.0)    
   1. Correctif logiciel pour le [problème de l’agent de surveillance concernant une utilisation intensive de l’UC avec les versions de la Base de connaissances .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
*   Agent Azure AD Connect Health pour AD FS (version 3.1.7.0)  
   1. Correctif logiciel pour le [problème de l’agent de surveillance concernant une utilisation intensive de l’UC avec les versions de la Base de connaissances .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   2. Correctifs de résultats de tests sur le serveur secondaire ADFS Server 2016
   
*   Agent Azure AD Connect Health pour AD FS (version 3.1.2.0)  
   1. Correctif logiciel pour la gestion de la mémoire des agents et alertes associées spécifiquement pour la version 3.0.244.0


## <a name="may-2018"></a>Mai 2018
**Mise à jour de l’agent :**
*   Agent Azure AD Connect Health pour AD DS (version 3.0.244.0)
 1. Amélioration de la confidentialité de l’agent  
 2. Résolutions de bogues et améliorations générales

*   Agent Azure AD Connect Health pour AD FS (version 3.0.244.0)
 1. Service de diagnostic de l’agent et améliorations associées du module PowerShell
 2. Amélioration de la confidentialité de l’agent  
 3. Résolutions de bogues et améliorations générales

* Agent Azure AD Connect Health pour la synchronisation (version 3.0.164.0) fourni avec Azure AD Connect version 1.1.819.0 
 1. Amélioration de la confidentialité de l’agent  
 2. Résolutions de bogues et améliorations générales


## <a name="march-2018"></a>Mars 2018
**Nouvelles fonctionnalités préliminaires :**
* Azure AD Connect Health pour AD FS - Rapport et alerte d’adresse IP à risque.

**Mise à jour de l’agent :**

*   Agent Azure AD Connect Health pour AD DS (version 3.0.176.0)
  1. Améliorations de la disponibilité de l’agent 
  2. Résolutions de bogues et améliorations générales
*   Agent Azure AD Connect Health pour AD FS (version 3.0.176.0)
  1. Améliorations de la disponibilité de l’agent 
  2. Résolutions de bogues et améliorations générales
* Agent Azure AD Connect Health pour la synchronisation (version 3.0.129.0) fourni avec Azure AD Connect version 1.1.750.0  
  1. Améliorations de la disponibilité de l’agent 
  2. Résolutions de bogues et améliorations générales

## <a name="december-2017"></a>Décembre 2017
**Mise à jour de l’agent :**

*   Agent Azure AD Connect Health pour AD DS (version 3.0.145.0)
  1. Améliorations de la disponibilité de l’agent 
  2. Ajout de nouvelles commandes de résolution des problèmes de l’agent
  3. Résolutions de bogues et améliorations générales
*   Agent Azure AD Connect Health pour AD FS (version 3.0.145.0)
  1. Ajout de nouvelles commandes de résolution des problèmes de l’agent
  2. Améliorations de la disponibilité de l’agent 
  3. Résolutions de bogues et améliorations générales
  
## <a name="october-2017"></a>Octobre 2017
**Mise à jour de l’agent :**

 * Agent Azure AD Connect Health pour la synchronisation (version 3.0.129.0) fourni avec Azure AD Connect version 1.1.649.0
<br></br> Résolution d’un problème de compatibilité entre Azure AD Connect et Azure AD Connect Health Agent pour la synchronisation. Ce problème affecte les clients qui effectuent une mise à niveau sur place d’Azure AD Connect vers la version 1.1.647.0, mais qui ont actuellement la version 3.0.127.0 de Health Agent. Après la mise à niveau, Health Agent ne peut plus envoyer les données d’intégrité sur le service de synchronisation Azure AD Connect à Azure AD Health Service. Avec ce correctif, Health Agent version 3.0.129.0 est installé lors de la mise à niveau sur place d’Azure AD Connect. Health Agent version 3.0.129.0 n’a pas de problème de compatibilité avec Azure AD Connect version 1.1.649.0.

## <a name="july-2017"></a>Juillet 2017
**Mise à jour de l’agent :**

*   Agent Azure AD Connect Health pour AD DS (version 3.0.68.0)
  1. Résolutions de bogues et améliorations générales
  2. Prise en charge du cloud souverain
*   Agent Azure AD Connect Health pour AD FS (version 3.0.68.0)
  1. Résolutions de bogues et améliorations générales
  2. Prise en charge du cloud souverain
* Agent Azure AD Connect Health pour la synchronisation (version 3.0.68.0) fourni avec Azure AD Connect version 1.1.614.0
  1. Prise en charge de Microsoft Azure Government Cloud et Microsoft Cloud Germany

## <a name="april-2017"></a>Avril 2017      
**Mise à jour de l’agent :**

*   Agent Azure AD Connect Health pour AD FS (version 3.0.12.0)
  1. Résolutions de bogues et améliorations générales
*   Agent Azure AD Connect Health pour AD DS (version 3.0.12.0)
  1. Améliorations du chargement des compteurs de performances
  2. Résolutions de bogues et améliorations générales

## <a name="october-2016"></a>Octobre 2016
**Mise à jour de l’agent :**

* Agent Azure AD Connect Health pour AD FS (version 2.6.408.0)
1. Amélioration de la détection des adresses IP clientes dans les demandes d’authentification
2. Résolution des bogues associés aux alertes
* Agent Azure AD Connect Health pour AD DS (version 2.6.408.0)
1. Résolution des bogues associés aux alertes
* Agent Azure AD Connect Health pour la synchronisation (version 2.6.353.0) fourni avec Azure AD Connect version 1.1.281.0
1. Ajout des données requises pour les rapports d’erreurs de synchronisation
2. Résolution des bogues associés aux alertes

**Nouvelles fonctionnalités préliminaires :**

* Rapports d’erreurs de synchronisation pour Azure AD Connect

**Nouvelles fonctionnalités :**

* Azure AD Connect Health pour AD FS - le champ d’adresse IP est disponible dans le rapport Top 50 des utilisateurs ayant saisi un nom d’utilisateur/mot de passe incorrect.

## <a name="july-2016"></a>Juillet 2016
**Nouvelles fonctionnalités préliminaires :**

* [Utilisation d’Azure AD Connect Health pour AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Janvier 2016
**Mise à jour de l’agent :**

* agent Azure AD Connect Health pour AD FS (version 2.6.91.1512)

**Nouvelles fonctionnalités :**

* [Outil de test de connectivité pour les agents Azure AD Connect Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembre 2015
**Nouvelles fonctionnalités :**

* Prise en charge du [Contrôle d’accès en fonction du rôle](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nouvelles fonctionnalités préliminaires :**

* [Azure AD Connect Health pour la synchronisation](how-to-connect-health-sync.md).

**Problèmes résolus :**

* Correctifs pour les erreurs survenant pendant les enregistrements d’agent.

## <a name="september-2015"></a>Septembre 2015
**Nouvelles fonctionnalités :**

* Rapport incorrect de mot de passe/nom d’utilisateur pour AD FS
* Prise en charge pour configurer le serveur proxy HTTP non authentifié
* Prise en charge pour configurer l’agent sur Server core
* Améliorations apportées aux alertes pour AD FS
* Améliorations dans Azure Agent AD Connect Health pour AD FS pour la connectivité et le chargement des données.

**Problèmes résolus :**

* Correctifs de bogues dans les informations d’utilisation pour les types d’erreur AD FS.

## <a name="june-2015"></a>Juin 2015
**Version initiale d’Azure AD Connect Health pour AD FS et le proxy AD FS.**

**Nouvelles fonctionnalités :**

* Alertes de surveillance des serveurs AD FS et de proxy AD FS avec les notifications par e-mail.
* Accès facile à la topologie et aux modèles AD FS dans les compteurs de performances AD FS.
* Tendances des demandes de jeton réussies sur les serveurs AD FS regroupés par applications, méthodes d’authentification, demande d’emplacement réseau, etc.
* Tendances des demandes ayant échoué sur les serveurs AD FS regroupés par applications, types d’erreur, etc.
* Déploiement d’agent plus simple à l’aide des informations d’identification d’administrateur général Azure AD.  

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](whatis-hybrid-identity-health.md).

