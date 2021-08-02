---
title: Obtenir des niveaux d’assurance relatifs à l’authentification de l’institut NIST avec Azure Active Directory
description: Vue d’ensemble de
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: ''
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500b22546089335563fd12953414aa74612cedf3
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537067"
---
# <a name="configure-azure-active-directory-to-meet-nist-authenticator-assurance-levels"></a>Configurer Azure Active Directory pour répondre aux niveaux d’assurance relatifs à l’authentification du NIST

Il est plus compliqué de fournir des services aux agences fédérales en raison du nombre et de la complexité des normes que vous devez respecter. En tant que fournisseur de services cloud (CSP, Cloud Service Provider) ou agence fédérale, il vous incombe de garantir la conformité avec toutes les normes en vigueur. Azure et Azure Active Directory (Azure AD) facilitent cette tâche en vous permettant d’utiliser nos certifications, puis de configurer vos besoins spécifiques.

Azure est certifié pour plus de 90 offres de conformité au moment de la rédaction de cet article. Pour plus d’informations, consultez [Faire confiance à votre Cloud](https://azure.microsoft.com/overview/trusted-cloud/).

## <a name="why-meet-nist-standards"></a>Pourquoi respecter les normes de l’institut NIST ? 

Le NIST (National Institute of Standards and Technology) développe les exigences techniques pour les agences fédérales américaines qui implémentent des solutions d’identité. Les organisations travaillant avec les agences fédérales doivent également répondre à ces exigences. Pour plus d’informations sur les exigences relatives aux identités du NIST, consultez la [Publication spéciale 800-63 révision 3](https://pages.nist.gov/800-63-3/sp800-63-3.html) (NIST SP 800-63-3).

La publication NIST SP 800-63 est également référencée par :
* Le programme [ECPS](https://deadiversion.usdoj.gov/ecomm/e_rx/) Electronic Prescription of Controlled Substances. 
* [Exigences de l’autorité FINRA (Financial Industry Regulatory Authority)](https://www.finra.org/rules-guidance). 
* Les secteurs de la santé et de la défense, ainsi que des associations d’autres secteurs d’activité, se servent souvent de la publication NIST SP 800-63-3 comme base de référence pour les exigences en matière de gestion des identités et des accès.

Les directives de l’institut NIST sont référencées dans d’autres normes, notamment dans le programme FedRAMP (Federal Risk and Authorization Management Program) destiné aux fournisseurs de solutions cloud. Azure est certifié pour le niveau d’impact élevé FedRAMP High Impact. 

Les directives sur l’identité numérique de l’institut NIST couvrent la vérification et l’authentification des utilisateurs, tels que les employés, les partenaires, les fournisseurs, les clients ou les citoyens. 

Les directives sur l’identité numérique NIST SP 800-63-3 englobent trois domaines :

* [SP 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html) couvre l’inscription et la vérification de l’identité.

* [SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) couvre l’authentification et la gestion de cycle de vie.

* [SP 800-63C](https://pages.nist.gov/800-63-3/sp800-63c.html) couvre la fédération et les assertions.

Chaque domaine a défini des niveaux d’assurance. Cet article fournit des conseils pour atteindre les niveaux d’assurance relatifs à l’authentification (AAL, Authenticator Assurance Level) comme décrit dans la publication NIST SP 800-63B à l’aide d’Azure AD et d’autres solutions Microsoft.

## <a name="next-steps"></a>Étapes suivantes 

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md) 
