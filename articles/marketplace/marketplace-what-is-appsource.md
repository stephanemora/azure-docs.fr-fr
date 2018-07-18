---
title: Présentation d’AppSource et fonctionnement avec Azure
description: Présentation d’AppSource, qui permet aux partenaires Microsoft de présenter leurs technologies et leurs services aux clients dans une vitrine en ligne prise en charge par Microsoft.
services: Marketplace, AppSource, Compute, Storage, Networking, Security, SaaS
documentationcenter: ''
author: ellacroi
manager: ''
editor: v-brela
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/12/2018
ms.author: ellacroi
ms.openlocfilehash: 7da3ae1ca0fc59b0d606a22e9c00b8e7b58674ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335089"
---
# <a name="what-is-appsource"></a>Présentation d’Appsource
AppSource permet aux partenaires Microsoft de présenter leurs technologies et leurs services aux clients dans une vitrine en ligne prise en charge par Microsoft. En tant qu’éditeur AppSource, vous pouvez encourager les utilisateurs professionnels à rechercher, essayer et acheter vos applications métier SaaS et vos services d’implémentation, en vue d’accroître leurs résultats commerciaux et de réduire leur délai de rentabilité : 

| Besoin du client       | AppSource          | 
|:---------------------------------------- |:----------------------------------------------------- |
| **Recherche des solutions d’entreprise qui fonctionnent avec les produits Microsoft qu’il utilise déjà**    | Permet aux partenaires d’utiliser des applications et des services tiers pour étendre les technologies et les applications cloud de Microsoft. |
| **Capacité à trouver facilement la solution ou le service d’implémentation adéquat.**     | Permet, en un seul et même endroit, de découvrir, d’essayer et d’obtenir des applications, des services, des compléments, et bien plus encore. |
| **Solution métier propre à un secteur permettant de répondre à des besoins spécifiques**   | Fournit des solutions de bout en bout complètes pour répondre aux exigences spécifiques de nombreux secteurs d’activité. |
| **Applications visant à améliorer la productivité, l’efficacité et les insights de l’activité**       | Fournit des applications métier, notamment pour le service clientèle, les ressources humaines, les opérations, et bien plus encore. |
| **Partenaire d’implémentation expérimenté pour aider à adapter les applications à la situation unique du client**     | Fournit un catalogue d’offres de services de conseil pour les solutions basées sur Dynamics 365, Power BI, PowerApps et sur des applications tierces, qui sont disponibles sur AppSource et qui ont pour but d’aider les utilisateurs professionnels à trouver des services de conseil leur permettant de parvenir à des résultats prévisibles. |

## <a name="appsource-publishing"></a>Publication sur AppSource
Sur AppSource, vous pouvez répertorier une application ou une offre de conseil permettant de répondre aux besoins des clients qui utilisent les produits Microsoft tels qu’Office 365, Dynamics 365, Power BI et Power Apps. Les offres suivantes sont disponibles sur la Place de marché AppSource : 
*   **Dynamics 365 for Finance and Operations** : solution ERP (Enterprise Resource Planning). Les solutions différenciées sont l’édition Entreprise pour les entreprises de taille moyenne à grande, et l’Édition Business pour les petites et moyennes entreprises.
*   **Dynamics 365 for Customer Engagement** : solution de gestion de la relation client (CRM) comprenant les applications Sales, Customer Service, Field Service et Project Service Automation.
*   **Dynamics NAV Managed Service** : Microsoft **Dynamics NAV** est une suite logicielle ERP pour les entreprises de taille moyenne. Ce service offre des fonctionnalités spécialisées pour la fabrication, la distribution, le secteur public, le commerce et pour d’autres secteurs.
*   **Power BI** : solution de visualisation des analyses marketing.
*   **Offres de conseil** : services fournis par les partenaires Microsoft, qui permettent aux clients de comprendre, tester et implémenter des solutions technologiques spécialisées.
*   **Cortana Intelligence** : Cortana Intelligence Suite regroupe des outils de plateforme de données et d’analytique Azure qui sont indépendants, mais entièrement intégrés.
*   **Office 365** : espace de travail dans le cloud. Travaillez en collaboration gratuitement avec Microsoft Word, PowerPoint, Excel et OneNote.

### <a name="office-365"></a>Office 365
Les applications pour Office sont désormais disponibles sur AppSource. Consultez le [processus et les instructions de publication]( https://docs.microsoft.com/office/dev/store/submit-to-the-office-store "Office 365 - AppSource").

### <a name="dynamics-365-for-finance-and-operations"></a>Dynamics 365 pour la finance et les opérations
Si vous créez des applications pour l’édition Entreprise, consultez le [processus et les instructions de publication](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source "Dynamics 365 Édition Entreprise - AppSource").  

### <a name="dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement
Consultez le [processus et les instructions de publication](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource "Dynamics 365 - AppSource").

### <a name="power-bi"></a>Power BI
Créez des visuels personnalisés et publiez-les via AppSource. Consultez le [processus et les instructions de publication](https://docs.microsoft.com/power-bi/developer/office-store "Power BI - AppSource").

### <a name="consulting-offers"></a>Offres de conseil
Si vous proposez des services destinés à faciliter la transition numérique ou l’implémentation d’une solution ou d’une application, vous pouvez les proposer sous forme d’offres de conseil sur AppSource. 
[Lisez les instructions pour savoir comment soumettre votre offre](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf "AppSource - Instructions de publication à destination des partenaires").

### <a name="cortana-intelligence"></a>Cortana Intelligence
[Guide de publication AppSource pour Cortana Intelligence](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide " AppSource Cortana Intelligence - Guide de publication")

## <a name="azure-active-directory-integration"></a>Intégration d'Azure Active Directory
Certaines applications de la boutique AppSource doivent être intégrées à Azure Active Directory (Azure AD) pour être publiées. L’intégration des applications à Azure AD est bien documentée. Microsoft fournit plusieurs SDK et d’autres ressources pour répondre à vos besoins. 

| Nom de l’offre | Intégration à Azure AD nécessaire | Notes |
|:--- |:---:|:--- |
| Dynamics 365 for Operations | Non  | Les offres disponibles sous licence ne nécessitent pas d’intégration à Azure AD. |
| Dynamics 365 for Finance | Non  | Les offres disponibles sous licence ne nécessitent pas d’intégration à Azure AD. |
| Dynamics 365 for Customer Engagement | Non  | Les offres disponibles sous licence ne nécessitent pas d’intégration à Azure AD. |
| Dynamics NAV Managed Service | Non  | Les offres disponibles sous licence ne nécessitent pas d’intégration à Azure AD. |
| Power BI | Non  | Les offres disponibles sous licence ne nécessitent pas d’intégration à Azure AD. |
| Offres de conseil | Non  | Services coordonnés par le partenaire, et non via le Web |
| Cortana Intelligence | OUI | L’utilisation d’Azure AD constitue une bonne pratique permettant de garantir une expérience utilisateur fluide et de générer des prospects de meilleure qualité. |
| SaaS Apps (anciennement Web Apps) | Recommandée | L’utilisation d’Azure AD constitue une bonne pratique permettant de garantir une expérience utilisateur fluide et de générer des prospects de meilleure qualité. |
###### <a name="table-appsource-offerings-that-require-azure-active-directory-integration"></a>Tableau : Offres AppSource qui nécessitent une intégration à Azure Active Directory

Pour démarrer, nous vous recommandons de **configurer un abonnement dédié à la publication sur la Place de marché Azure**\*, de manière à pouvoir isoler votre travail des autres initiatives. De plus, si vous n’avez pas déjà installé les outils suivants, ajoutez-les à votre environnement de développement : 
*   [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest "Azure - CLI")
*   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0 "PowerShell - Azure")
*   Passez en revue les outils disponibles dans la page [Outils de développement Azure](https://azure.microsoft.com/tools/ "Azure - Outils de développement").
*   [Visual Studio Code](https://code.visualstudio.com/ "Visual Studio Code - Page principale")

Si vous débutez avec Azure AD, cliquez sur les liens suivants pour consultez les ressources dont vous aurez besoin : 

**Documentation**
*   [Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide "Azure AD - Guide du développeur")
*   [Intégration à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate "Intégration - Azure AD")
*   [Intégration d’applications à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications "Intégration d’applications - Azure AD")
*   [Feuille de route Azure - Sécurité et identité](https://azure.microsoft.com/roadmap/?category=security-identity "Feuille de route Azure - Sécurité et identité")

**Vidéos**
*   [Authentification Azure Active Directory avec Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration "Authentification Azure Active Directory - Vittorio Bertocci")
*   [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration "Azure AD - Identity Technical Briefing 1/2")
*   [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration "Azure AD - Identity Technical Briefing 2/2")
*   [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration "Building Apps - Azure AD")
*   [Microsoft Azure Videos focused on Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory "Azure AD - Vidéos")

**Formation**
*   [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965 "Microsoft Azure for IT Pros Content Series: Azure AD")

**Mises à jour du service Azure Active Directory**
*   [Mises à jour du service Azure AD](https://azure.microsoft.com/updates/?product=active-directory "Azure AD - Mises à jour du service")

Pour la prise en charge, les liens suivants constituent de bonnes ressources : 
*   Forums MSDN : [Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD "Azure AD dans les forums MSDN")
*   StackOverflow : [Azure Active Directory](https://stackoverflow.com/questions/tagged/azure-active-directory "Azure AD sur StackOverflow")
