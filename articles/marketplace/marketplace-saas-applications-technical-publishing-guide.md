---
title: Guide de publication technique de l’application SaaS Place de marché Microsoft Azure
description: Guide étape par étape et listes de contrôle de publication pour la publication des applications SaaS sur la Place de marché Azure
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809469"
---
# <a name="saas-applications-technical-publishing-guide"></a>Guide de publication technique de l’application SaaS

Bienvenue dans le guide de publication technique de l’application SaaS Place de marché Microsoft Azure. Ce guide est conçu pour aider les éditeurs candidats et existants à répertorier leurs applications et services dans la Place de marché Azure en utilisant l’offre des applications SaaS. 

Pour mieux comprendre comment publier une offre SaaS, ce guide est divisé en sections, à savoir :
* Vue d’ensemble de l’offre
* Exigences commerciales
* Exigences techniques
* Processus de publication
* Utilisation d’Azure Active Directory pour activer les versions d’évaluation
* Certification de votre intégration Azure Active Directory pour la Place de marché

## <a name="offer-overview"></a>Vue d’ensemble de l’offre  

Des applications SaaS sont disponibles dans les deux vitrines Azure. Le tableau suivant décrit les options actuellement disponibles :

| Option de vitrine | Énumération | Essai/Transaction |  
| --- | --- | --- |  
| AppSource | Oui (Me contacter) | Oui (Power BI/Dynamics) |
| Place de marché Azure | Non  | Oui (Applications SaaS) |   

**Liste :** l’option de publication Liste consiste en une offre de type Me contacter, et est utilisée quand une participation de niveau Essai ou Transaction n’est pas possible. L’avantage de cette méthode est qu’elle permet aux éditeurs commercialisant une solution de commencer immédiatement à recevoir des prospects convertibles en transactions pour développer leur activité.  
**Essai/Transaction :** le client a la possibilité d’acheter directement votre solution ou de demander une version d’évaluation de celle-ci. Proposer une version d’évaluation augmente le niveau d’engagement des clients en leur permettant de découvrir votre solution avant de l’acheter. Avec une expérience d’évaluation, vous augmentez vos chances d’être mieux référencé dans les vitrines, et donc d’obtenir des prospects en plus grand nombre et plus intéressants. Les versions d’essai doivent fournir un support gratuit, au moins pendant la période d’évaluation.  

| Offre d’applications SaaS | Exigences commerciales | Exigences techniques |  
| --- | --- | --- |  
| **Nous contacter** | OUI | Non  |  
| **Power BI / Dynamics** | OUI | Oui (Intégration Azure AD) |  
| **Applications SaaS**| OUI | Oui (Intégration Azure AD) |     

Pour plus d’informations sur les vitrines de la Place de marché et une description de chaque option de publication, voir le [Guide de publication sur la Place de marché](https://aka.ms/sellerguide) et les [Options de publication](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Exigences commerciales
Les exigences de l’entreprise en lien avec l’offre SaaS peuvent être remplies parallèlement aux exigences techniques. La plupart des exigences et informations de l’entreprise sont collectées lors de la création de l’offre SaaS dans le portail Cloud Partner. Les exigences commerciales sont les suivantes : 
* Acceptation des stratégies de participation
* Intégration avec Microsoft 
* Identification du public cible de l’offre
* Définition et détermination de la gestion des prospects à mettre en œuvre
* Définition de politique de confidentialité et de conditions d’utilisation
* Définition des contacts de support  

Pour plus d’informations, voir la rubrique [Prérequis pour la publication sur la Place de marché](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Exigences techniques

Les exigences techniques pour les applications SaaS sont simples. Pour être publiés, les éditeurs sont uniquement tenus d’être intégrés avec Azure Active Directory (Azure AD). L’intégration d’Azure AD aux applications est bien documentée et Microsoft fournit plusieurs kits SDK et ressources pour effectuer cette opération.  

Pour démarrer, nous vous recommandons d’avoir un abonnement dédié pour la publication sur la Place de marché Azure, ce qui vous permet d’isoler la tâche des autres initiatives. Cela fait, vous pouvez commencer à déployer votre application SaaS dans cet abonnement pour démarrer le travail de développement.  

La meilleure documentation, les meilleurs exemples et les meilleurs conseils concernant Azure Active Directory figurent sur les sites suivants : 

* [Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Intégration à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Feuille de route Azure - Sécurité et identité](https://azure.microsoft.com/roadmap/?category=security-identity)

Pour des didacticiels vidéos, suivez les liens suivants :

* [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Vidéos Microsoft Azure axées sur Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Une formation gratuite à Azure Active Directory est accessible via le lien suivant :  
* [Série de formations sur Microsoft Azure destinées aux professionnels de l’informatique : Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

En outre, Azure Active Directory fournit un site pour vérifier la disponibilité de mises à jour du service :   
* [Mises à jour du service Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Pour le support, vous pouvez utiliser les ressources suivantes :
* [Forums MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Processus de publication

Le processus de publication SaaS comporte des étapes techniques et commerciales.  L’essentiel du travail de développement et d’intégration d’Azure Active Directory peut être effectué parallèlement au travail nécessaire pour répondre aux exigences commerciales de l’offre. La plupart des exigences commerciales font partie de la configuration de l’offre d’application SaaS sur le portail Cloud Partner.  
Le diagramme suivant montre les principales étapes de publication de l’offre Essai/Transaction :  

![Étapes de publication SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

Le tableau suivant décrit chacune des principales étapes de publication :  

| Étape de publication | Description |   
| --- | --- |  
| **Créer l’application SaaS** | Connectez-vous au portail Cloud Partner, sélectionnez **Nouveau**, puis choisissez l’offre **Applications SaaS**. |  
| **Créer l’intégration avec Azure AD** | Suivez les exigences techniques décrites dans la section précédente pour intégrer votre offre SaaS avec Azure AD. |  
| **Définir les paramètres de l’offre**| Entrez toutes les informations initiales de l’offre SaaS, à savoir l’ID et le nom d’offre que vous souhaitez utiliser. |     
| **Définir les informations techniques** | Entrez les informations techniques relatives à l’offre. Pour les applications SaaS, l’URI de la solution et le bouton de type d’acquisition de l’offre (Gratuit, Essai ou Me contacter) sont requis. |  
| **Version d’évaluation (facultatif)** | Il s’agit d’un type d’essai facultatif, nécessaire principalement pour d’autres types d’offres de la Place de marché. Il vous permet d’avoir la version d’évaluation déployée dans les abonnements de l’éditeur par opposition au client final. |  
| **Définir les éléments matériels de la vitrine de l’offre**| Dans cette section, l’éditeur lie et charge les logos, les supports marketing et les documents juridiques de l’offre, et configure le système de gestion des prospects. |
| **Définir les contacts de l’offre** | Entrez les coordonnées des contacts d’ingénierie et de support pour l’offre SaaS. |  
| **Vérifier l’intégration de l’application SaaS avec Azure AD** | Avant de soumettre votre application SaaS pour publication, vous devez vérifier qu’elle est intégrée avec Azure AD |  
| **Publier l’offre**| Une fois l’offre et les ressources techniques prêtes, vous pouvez envoyer l’offre. Cela a pour effet de démarrer le processus de publication dans le cadre duquel le modèle de solution est testé, validé, certifié et approuvé pour la publication. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Utilisation d’Azure Active Directory pour activer les versions d’évaluation  

Microsoft authentifie tous les utilisateurs de la Place de marché auprès d’Azure AD. Par conséquent, quand un utilisateur authentifié clique sur votre annonce de version d’essai sur la Place de marché et est redirigé vers votre environnement d’essai, vous pouvez approvisionner l’utilisateur directement dans un Essai, sans qu’aucune autre étape de connexion soit nécessaire. Le jeton que votre application reçoit d’Azure AD pendant l’authentification inclut des informations utilisateur utiles que vous pouvez utiliser pour créer un compte utilisateur dans votre application, ce qui vous permet d’automatiser l’expérience d’approvisionnement et d’augmenter la probabilité de conversion. Pour plus d’informations sur le jeton, voir [Exemples de jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

L’utilisation d’Azure AD pour activer l’authentification en un clic auprès de votre application effectue les opérations suivantes :  
* Simplifie l’expérience utilisateur, de la Place de marché à l’Essai.  
* Fournit une expérience d’utilisation réelle du produit, même quand l’utilisateur est redirigé de la Place de marché vers votre domaine ou votre environnement d’Essai.  
* Réduit la probabilité d’un abandon après redirection, car aucune autre étape d’authentification supplémentaire n’est nécessaire.  
* Réduit les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certification de votre intégration Azure Active Directory pour la Place de marché  

Vous pouvez certifier votre intégration avec Azure AD de différentes manières, selon que votre application est à client unique ou mutualisée, et que vous utilisez déjà ou découvrez l’authentification unique fédérée (SSO) Azure AD.  

**Pour les applications mutualisées :**  

Si vous prenez déjà en charge Azure AD, procédez comme suit :
1.  Inscrivez votre application dans le portail Azure
2.  Activez la fonctionnalité de prise en charge mutualisée dans Azure AD pour bénéficier de l’expérience d’essai en un clic. Des informations plus spécifiques sont accessibles [ici](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Si vous découvrez l’authentification unique fédérée Azure AD, procédez comme suit : 
1.  Inscrivez votre application dans le portail Azure
2.  Développez l’authentification unique auprès d’Azure AD en utilisant [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ou [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Activez la fonctionnalité de prise en charge mutualisée dans AAD pour bénéficier de l’expérience d’essai en 1 clic. Pour des informations plus spécifiques, voir [ici](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Pour une application à client unique, vous disposez des options suivantes :**  
* ajouter des utilisateurs à votre annuaire en tant qu’utilisateurs invités en utilisant [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) ;
* approvisionner manuellement les essais des clients via l’option « Me contacter » ;
* développer une « version d’évaluation » par client.
* Créer une application de démonstration multilocataire avec authentification unique

