---
title: Configurer Azure Active Directory pour atteindre un niveau d’impact FedRAMP High
description: Vue d’ensemble de la façon dont vous pouvez atteindre un niveau d’impact FedRAMP High pour votre organisation à l’aide d’Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 965fbe7ec1f46e006e30d721bf306a0debaf8473
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293966"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>Configurer Azure Active Directory pour atteindre un niveau d’impact FedRAMP High

Le [programme FedRAMP](https://www.fedramp.gov/) (Federal Risk and Authorization Management Program) est un processus d’évaluation et d’autorisation destiné aux fournisseurs de services cloud (CSP) qui créent des offres de solutions Cloud à utiliser avec des organismes fédéraux. Azure et Azure Government ont obtenu une [autorisation d’exploitation provisoire (P-ATO, Provisional Authority to Operate) de niveau d’impact élevé](https://docs.microsoft.com/compliance/regulatory/offering-fedramp) auprès du Joint Authorization Board, l’échelon le plus haut en matière d’accréditation FedRAMP.

Azure fournit la possibilité de satisfaire à toutes les exigences de contrôle, afin d’obtenir une qualification élevée FedRAMP pour votre offre de solutions Cloud, ou en tant qu’organisme fédéral. Il incombe à votre organisation d’effectuer les configurations ou les processus supplémentaires pour être conforme. Cette responsabilité s’applique aux fournisseurs de services cloud qui recherchent une autorisation FedRAMP élevée pour leur offre de solutions cloud, et aux organismes fédéraux qui recherchent une autorisation d’exploitation (ATO, Authority to Operate). 

## <a name="microsoft-and-fedramp"></a>Microsoft et FedRAMP 

Microsoft Azure prend en charge plus de services aux niveaux d’[impact FedRAMP High](https://docs.microsoft.com/azure/azure-government/compliance/azure-services-in-fedramp-auditscope) que n’importe quel autre fournisseur de services cloud. Et même si FedRAMP High dans le cloud public Azure répond aux besoins de nombreux clients de la fonction publique des États-Unis, les organismes ayant des exigences plus rigoureuses peuvent s’appuyer sur le cloud Azure Government. Le cloud Azure Government apporte des protections supplémentaires, telles que le contrôle renforcé du personnel. 

Microsoft est tenu de renouveler la certification de ses services cloud tous les ans pour conserver ses autorisations. Pour cela, Microsoft supervise et évalue en permanence ses contrôles de sécurité, et fait la démonstration que la sécurité de ses services demeurent en conformité.

* [Autorisations FedRAMP des services cloud Microsoft](https://marketplace.fedramp.gov/)

* [Rapports d’audit FedRAMP de Microsoft](https://aka.ms/MicrosoftFedRAMPAuditDocuments)

Pour recevoir d’autres rapports FedRAMP, envoyez un e-mail à la [Documentation fédérale Azure](mailto:AzFedDoc@microsoft.com).

Plusieurs chemins mènent à l’autorisation FedRAMP. Vous pouvez réutiliser le package d’autorisation existant de Microsoft Azure, et les conseils fournis ici pour réduire de manière significative le temps et les efforts nécessaires à l’obtention d’une autorisation ATO ou P-ATO. Vous trouverez plus d’informations concernant FedRAMP sur le [site web FedRAMP.](https://www.fedramp.gov/)

 ## <a name="scope-of-guidance"></a>Étendue des recommandations

La publication FedRAMP High Baseline se compose de 421 contrôles et améliorations apportées aux contrôles issus du catalogue [NIST 800-53 Security Controls Catalog Revision 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final). Lorsque cela est possible, nous avons inclus des éclaircissements tirés de la publication [800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final). Cet ensemble d’articles définit une partie de ces contrôles qui sont liés à l’identité, et que vous devez configurer. Nous fournissons des instructions normatives pour vous aider à vous mettre en conformité avec les contrôles que vous êtes chargé de configurer dans Azure Active Directory (Azure AD). Pour répondre entièrement à certaines exigences de contrôle d’identité, vous devrez peut-être utiliser d’autres systèmes. Ces autres systèmes peuvent comporter un outil SIEM (Security information and Event Management), tel qu’Azure Sentinel. Si vous utilisez des services Azure en dehors d’Azure Active Directory, vous devez prendre en compte d’autres contrôles ; vous pouvez également utiliser les fonctionnalités qu’Azure a déjà mis en place pour satisfaire aux contrôles.

Ressources FedRAMP

* [Federal Risk and Authorization Management Program](https://www.fedramp.gov/)

* [FedRAMP Security Assessment Framework](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [Agency Guide for FedRAMP Authorizations](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [Gestion de la conformité dans le cloud chez Microsoft](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Cloud de Microsoft pour le Secteur Public](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Offres de conformité Azure](https://aka.ms/azurecompliance)

* [Vue d’ensemble de l’exemple de blueprint FedRAMP High](https://docs.microsoft.com/azure/governance/blueprints/samples/fedramp-h/)

* [Centre de conformité Microsoft 365](https://docs.microsoft.com///microsoft-365/compliance/microsoft-365-compliance-center)

* [Gestionnaire de conformité Microsoft](https://docs.microsoft.com///microsoft-365/compliance/compliance-manager)

 

## <a name="next-steps"></a>Étapes suivantes

[Configurer des contrôles d’accès](fedramp-access-controls.md)

[Configurer les contrôles d’identification et d’authentification](fedramp-identification-and-authentication-controls.md)

[Configurer d’autres contrôles](fedramp-other-controls.md)

 
