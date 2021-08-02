---
title: Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD
description: Cet article décrit les conditions préalables requises et la configuration matérielle requise pour l’utilisation de l’Hôte de connecteur ECMA Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c668380103088fb0f434d513cf7490c39e4ead2
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570247"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Conditions préalables requises pour l’Hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion de l’approvisionnement local n’est disponible que sur invitation. Vous pouvez demander l’accès à cette fonctionnalité [ici](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Cet article fournit de l’aide sur les conditions préalables requises pour l’utilisation de l’Hôte de connecteur ECMA Azure AD.  

L’installation et la configuration de l’Hôte de connecteur ECMA Azure AD sont un processus. Utilisez le flux ci-dessous pour vous guider tout au long du processus.

 ![Flux d’installation](./media/on-premises-ecma-prerequisites/flow-1.png)  

Pour plus d’informations sur l’installation et la configuration, consultez :
   - [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
   - [Configurer l’Hôte de connecteur ECMA Azure AD et l’agent d’approvisionnement](on-premises-ecma-configure.md)
   - [Configuration du connecteur SQL générique pour l’Hôte de connecteur ECMA Azure AD](on-premises-sql-connector-configure.md)

## <a name="on-premises-pre-requisites"></a>Conditions préalables locales requises
 - Système cible, tel qu’une base de données SQL, dans lequel les utilisateurs peuvent être créés, mis à jour et supprimés.
 - Connecteur ECMA 2.0 ou une version ultérieure pour ce système cible, qui prend en charge l’exportation, la récupération de schéma et éventuellement les opérations d’importation delta ou d’importation complètes. Si vous ne disposez pas de connecteur ECMA pendant la configuration, vous pouvez toujours valider le flux de bout en bout si vous disposez d’un SQL Server dans votre environnement et que vous utilisez le connecteur SQL générique.
 - Ordinateur Windows Server 2016 ou version ultérieure disposant d’une adresse TCP/IP accessible via Internet, d’une connectivité au système cible et d’une connectivité sortante vers login.microsoftonline.com (par exemple, une machine virtuelle Windows Server 2016 hébergée dans Azure IaaS ou derrière un proxy). Le serveur doit disposer d’au moins 3 Go de RAM.
 - Ordinateur avec .NET Framework 4.7.1

## <a name="cloud-requirements"></a>Conditions préalables requises du cloud

 - Locataire Azure AD avec Azure AD Premium P1 ou Premium P2 (ou EMS E3 ou E5). 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]

 - Rôle Administrateur hybride pour la configuration de l’agent d’approvisionnement et rôles Administrateur d’application ou Administrateur cloud pour la configuration de l’approvisionnement dans le Portail Azure.


## <a name="next-steps"></a>Étapes suivantes

- [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
- [Configuration de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-configure.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
- [Tutoriel : Connecteur SQL générique pour l’Hôte de connecteur ECMA](tutorial-ecma-sql-connector.md)
