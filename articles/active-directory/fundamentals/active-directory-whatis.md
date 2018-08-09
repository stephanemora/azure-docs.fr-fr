---
title: Qu’est-ce qu’Azure Active Directory ?(Azure AD) | Microsoft Docs
description: Azure Active Directory permet d'étendre vos identités locales existantes dans le cloud ou de développer des applications intégrées Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449992"
---
# <a name="what-is-azure-active-directory"></a>Qu’est-ce qu’Azure Active Directory ?
Azure Active Directory (Azure AD) est le service mutualisé et reposant sur le cloud de gestion des identités et de répertoire Microsoft qui combine les principaux services d’annuaire, la gestion des accès aux applications et la protection des identités dans une seule solution. Azure AD offre également une riche plateforme reposant sur des standards, qui permet aux développeurs de contrôler l’accès à leurs applications, grâce à des stratégies et des règles centralisées.

![Pile Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Pour les administrateurs informatiques.** Azure AD fournit une solution plus sécurisée pour votre organisation via l’utilisation d’une gestion des identités et d’un accès avec authentification unique renforcés à des milliers [d’applications SaaS cloud](../saas-apps/tutorial-list.md) et d’applications locales. Via ces applications, vous bénéficierez également d’une sécurité des applications cloud, d’un accès transparent, d’une meilleure collaboration et d’une automatisation du cycle de vie des identités de vos employés pour améliorer la sécurité et la conformité.

    De plus, en [quatre clics](./../connect/active-directory-aadconnect-get-started-express.md) seulement, vous pouvez intégrer Azure AD à une instance existante de Windows Server Active Directory, ce qui offre à votre organisation la possibilité d’exploiter ses investissements d’identité locale pour gérer l’accès aux applications SaaS basées sur le cloud.

- **Pour les développeurs PHP.** Azure AD vous permet de vous concentrer sur la création de vos applications en vous offrant l’intégration d’une solution de gestion des identités utilisée par des millions d’organisations sur toute la planète.

- **Pour les clients Office 365, Azure ou Dynamics CRM Online.** Vous utilisez déjà Azure AD. Chaque locataire Office 365, Azure et Dynamics CRM Online est en fait un locataire Azure AD, ce qui vous permet de commencer immédiatement à gérer votre accès employé à vos applications cloud intégrées.

## <a name="how-reliable-is-azure-ad"></a>Quelle est la fiabilité d’Azure AD ?
La conception d’Azure AD pour l’architecture mutualisée géolocalisée à haute disponibilité signifie que vous pouvez vous reposer sur cette solution pour répondre à vos besoins les plus critiques. L’exécution de 28 centres de données dans le monde entier avec basculement automatisé vous offre le confort de savoir qu’Azure AD est hautement disponible et que, même si un centre de données tombe en panne, des copies de vos données d’annuaire sont disponibles dans des centres de données situés dans au moins deux régions, pour un accès instantané.

Pour plus d’informations sur les contrats de niveau de service, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Choisir une édition
Tous les services d'entreprise Microsoft Online reposent sur Azure Active Directory (Azure AD) pour l'authentification et autres besoins d’identification. Si vous vous abonnez à un service d’entreprise Microsoft Online quelconque (par exemple, Office 365 ou Microsoft Azure), vous obtenez Azure AD avec un accès à toutes les fonctionnalités gratuites. L’édition gratuite d’Azure Active Directory vous permet de gérer les utilisateurs et les groupes, d’effectuer une synchronisation avec les annuaires locaux, d’obtenir l’authentification unique entre Azure, Office 365 et des milliers d’applications SaaS populaires telles que Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. 

Pour enrichir votre expérience Azure Active Directory, vous pouvez ajouter des fonctionnalités payées à l'aide des éditions Azure Active Directory de base, Premium P1 et Premium P2. Les versions payées Azure Active Directory se superposent à votre annuaire gratuit existant et vous offrent de nombreuses fonctionnalités de classe entreprise : libre-service, surveillance améliorée, rapports de sécurité, authentification multifacteur MFA (Multi-Factor Authentication) et accès sécurisé pour votre personnel mobile.

> [!NOTE]
> Pour les options de tarification de ces éditions, consultez la [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Les éditions Azure Active Directory, Premium P1, Premium P2 et Azure Active Directory de base ne sont actuellement pas prises en charge en Chine. Pour plus d’informations sur la tarification Azure AD, vous pouvez contacter le Forum Azure Active Directory.
>

* **Azure Active Directory Basic** : conçue pour les employés avec des besoins principalement centrés sur le cloud, cette édition offre un accès aux applications avec des besoins principalement centrés sur le cloud, ainsi que des solutions de gestion des identités en libre-service. Avec l’édition de base d’Azure Active Directory, vous disposez de fonctionnalités qui améliorent votre productivité et réduisent vos coûts comme la gestion d’accès basée sur les groupes, la réinitialisation libre-service du mot de passe pour les applications cloud et un proxy d’application Azure Active Directory (pour publier des applications web locales à l’aide d’Azure Active Directory). Le tout, avec un contrat SLA pour professionnel qui fournit 99,9 % de disponibilité.
* **Azure Active Directory - version Premium P1** : Conçue pour permettre aux entreprises avec des besoins d’identification et de gestion des accès plus élevés, la version Premium d’Azure Active Directory ajoute des fonctionnalités de gestion des identités de niveau professionnel et permet aux utilisateurs hybrides d'accéder en toute transparence aux fonctionnalités locaux et disponibles sur le cloud. Cette édition inclut tout ce dont vous avez besoin pour les travailleurs de l’information et les administrateurs d’identités dans les environnements hybrides grâce à un accès inter-applications, à une gestion des identités et de l’accès en libre-service (IAM), à la protection des identités et à la sécurité dans le cloud. Elle prend en charge des ressources d'administration et de délégation avancées telles que des groupes dynamiques et la gestion de groupes en libre-service. Elle inclut Microsoft Identity Manager (une suite de gestion locale des identités et des accès) et fournit des fonctionnalités d'écriture différée sur le cloud offrant aux utilisateurs locaux des solutions comme la réinitialisation en libre-service du mot de passe du service.
* **Azure Active Directory Premium P2** : conçue avec la protection avancée pour tous vos utilisateurs et administrateurs, cette nouvelle offre inclut toutes les fonctionnalités d’Azure AD Premium P1, ainsi que les solutions Identity Protection et Privileged Identity Management. Active Directory Azure Identity Protection exploite des milliards de signaux pour fournir un accès conditionnel basé sur les risques à vos applications et données d’entreprise critiques. Nous vous aidons également à gérer et protéger les comptes privilégiés avec Azure Active Directory Privileged Identity Management afin que vous puissiez détecter, restreindre et surveiller les administrateurs et leur accès aux ressources, et fournir un accès juste-à-temps quand cela est nécessaire.  

> [!NOTE]
> Plusieurs fonctionnalités Azure Active Directory sont disponibles via les éditions de « paiement à l’utilisation » :
>
> * Active Directory B2C constitue la solution de gestion des identités et des accès pour vos applications grand public. Pour plus d’informations, consultez [Documentation Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
> * L'authentification multifacteur MFA (Multi-Factor Authentication) Azure peut être effectuée par utilisateur ou par le biais de fournisseurs d'authentification. Pour plus d’informations, consultez [Présentation d’Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).
>

## <a name="how-can-i-get-started"></a>Comment faire pour démarrer ?

**Si vous êtes un administrateur informatique :**

* [Faites un essai.](https://azure.microsoft.com/trial/get-started-active-directory/) Vous pouvez vous inscrire pour une période d’essai gratuit de 30 jours dès aujourd’hui, puis déployer votre première solution cloud en moins de cinq minutes à l’aide de ce lien

* Lisez la page [Prise en main d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) pour accélérer l’installation et la configuration d’un client Azure AD.

**Si vous êtes un développeur :**
 
* Consultez le [Guide pour les développeurs](../develop/azure-ad-developers-guide.md) dédié à Azure Active Directory

* [Démarrer une version d’évaluation](https://azure.microsoft.com/trial/get-started-active-directory/) : abonnez-vous à une version d’essai gratuite pendant 30 jours dès aujourd’hui et commencez à intégrer vos applications à Azure AD

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les principes fondamentaux de la gestion des identités et des accès Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
