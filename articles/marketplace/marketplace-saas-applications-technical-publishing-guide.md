---
title: Guide de publication technique de l’application SaaS Place de marché Microsoft Azure
description: Guide étape par étape et listes de contrôle de publication pour la publication des applications SaaS sur la Place de marché Azure
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 2c1cb755b62812336a306994f6820573130815e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288357"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guide de publication de l’offre des applications SaaS

Des applications SaaS peuvent être publiées sur la place de marché avec trois différents appels à l’action : « Me contacter », « Essayer maintenant » et « Obtenir maintenant ». Ce guide détaille ces trois options, y compris les exigences propres à chacune. 

## <a name="offer-overview"></a>Vue d’ensemble de l’offre  

Des applications SaaS sont disponibles dans les deux vitrines Azure. Le tableau suivant décrit les options actuellement disponibles :

| Option de vitrine | Énumération | Essai/Transaction |  
| --- | --- | --- |  
| AppSource | Oui (Me contacter) | Oui (Power BI/Dynamics) |
| Place de marché Azure | Non | Oui (Applications SaaS) |   

**Liste :** L’option de publication Liste consiste en une offre de type Me contacter et est utilisée quand une participation de niveau Essai ou Transaction n’est pas possible. L’avantage de cette méthode est qu’elle permet aux éditeurs commercialisant une solution de commencer immédiatement à recevoir des prospects convertibles en transactions pour développer leur activité.  
**Essai/Transaction :** Le client a la possibilité d’acheter directement votre solution ou de demander une version d’essai de celle-ci. Proposer une version d’évaluation augmente le niveau d’engagement des clients en leur permettant de découvrir votre solution avant de l’acheter. Avec une expérience d’évaluation, vous augmentez vos chances d’être mieux référencé dans les vitrines, et donc d’obtenir des prospects en plus grand nombre et plus intéressants. Les versions d’essai doivent fournir un support gratuit, au moins pendant la période d’évaluation.  

| Offre d’applications SaaS | Exigences commerciales | Exigences techniques |  
| --- | --- | --- |  
| **Nous contacter** | Oui | Non |  
| **Power BI / Dynamics** | Oui | Oui (Intégration Azure AD) |  
| **Applications SaaS**| Oui | Oui (Intégration Azure AD) |     

## <a name="saas-list"></a>Liste de SaaS

L’appel à l’action pour obtenir la liste de SaaS sans version d’essai ni fonctionnalité de facturation est « Me contacter ». 

Vous n’avez pas besoin de configurer Azure Active Directory pour répertorier une application SaaS. 

|Spécifications  |Détails  |
|---------|---------|
|Votre application est une offre SaaS  |   Votre solution est une offre SaaS et vous proposez un produit SaaS mutualisé.      |


## <a name="saas-trial"></a>Version d’essai SaaS

Vous fournissez une solution ou une application par le biais d’une version d’évaluation SaaS gratuite. Les offres d’essai gratuit peuvent être proposées sous la forme d’un compte d’essai à durée ou utilisation limitée. 


|Spécifications  |Détails  |
|---------|---------|
|Votre application est une offre SaaS  |   Votre solution est une offre SaaS et vous proposez un produit SaaS mutualisé.      |
|Votre application est compatible avec AAD     |   Le client sera redirigé vers votre domaine et vous effectuerez des transactions directement avec le client.       |


## <a name="saas-trial-technical-requirements"></a>Exigences techniques des versions d’essai SaaS

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
* [Série de formations sur Microsoft Azure destinées aux professionnels de l’informatique : Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

En outre, Azure Active Directory fournit un site pour vérifier la disponibilité de mises à jour du service :   
* [Mises à jour du service Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Utilisation d’Azure Active Directory pour activer les versions d’évaluation  

Microsoft authentifie tous les utilisateurs de la Place de marché auprès d’Azure AD. Ainsi, quand un utilisateur authentifié clique sur votre annonce de version d’essai sur la Place de marché et est redirigé vers votre environnement d’essai, vous pouvez provisionner l’utilisateur directement dans un Essai, sans qu’aucune autre étape de connexion soit nécessaire. Le jeton que votre application reçoit d’Azure AD pendant l’authentification inclut des informations utilisateur utiles que vous pouvez utiliser pour créer un compte utilisateur dans votre application, ce qui vous permet d’automatiser l’expérience d’approvisionnement et d’augmenter la probabilité de conversion. Pour plus d’informations sur le jeton, voir [Exemples de jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

L’utilisation d’Azure AD pour activer l’authentification en un clic auprès de votre application effectue les opérations suivantes :  
* Simplifie l’expérience utilisateur, de la Place de marché à l’Essai.  
* Fournit une expérience d’utilisation réelle du produit, même quand l’utilisateur est redirigé de la Place de marché vers votre domaine ou votre environnement d’Essai.  
* Réduit la probabilité d’un abandon après redirection, car aucune autre étape d’authentification supplémentaire n’est nécessaire.  
* Réduit les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certification de votre intégration Azure Active Directory pour la Place de marché  

Certifiez votre intégration avec Azure AD de différentes manières, selon que votre application est à locataire unique ou multilocataire, et que vous utilisez déjà ou découvrez l’authentification unique fédérée (SSO) Azure AD.  

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

## <a name="saas-subscriptions"></a>Abonnements SaaS

Utilisez le type d’offre Application SaaS pour permettre à votre client d’acheter votre solution technique SaaS sous la forme d’un abonnement. Les conditions suivantes doivent être remplies pour votre application SaaS :
- Appliquez au service un tarif forfaitaire (mensuel ou annuel), ou un tarif par utilisateur.
- Offrez une méthode de mise à niveau ou d’annulation du service à tout moment.
Microsoft héberge la transaction commerciale. Microsoft facture votre client pour votre compte. Pour proposer une application SaaS sous la forme d’un abonnement, vous devez l’intégrer aux API de traitement SaaS.  Votre service doit prendre en charge le provisionnement, la mise à niveau et l’annulation.

| Condition requise | Détails |  
|:--- |:--- |  
|Facturation et mesure | Votre offre est facturée en fonction du modèle tarifaire que vous sélectionnez avant la publication (forfaitaire ou par utilisateur).  Si vous utilisez le modèle forfaitaire, vous pouvez inclure des dimensions supplémentaires afin de facturer aux clients une utilisation non comprise dans le forfait. |  
|Annulation | Votre offre peut être annulée par le client à tout moment. |  
|Page d’accueil de transaction | Vous hébergez une page d’accueil de transaction conjointe avec Azure, sur laquelle les utilisateurs peuvent créer et gérer leur compte de service SaaS. |   
| API d’abonnement | Vous exposez un service qui peut interagir avec l’abonnement SaaS pour créer, mettre à jour et supprimer un compte utilisateur et un plan de service. Les modifications critiques de l’API doivent être effectuées dans les 24 heures. Des modifications non critiques de l’API sont publiées régulièrement. |  

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](./cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait,

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Offre d’application Azure SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
