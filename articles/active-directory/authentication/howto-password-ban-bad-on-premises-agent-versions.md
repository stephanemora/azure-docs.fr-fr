---
title: Historique de publication des versions de l’agent de protection par mot de passe Azure AD local
description: Historique de publication des versions de documents et des changements de comportement
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50742904"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Préversion : Historique des versions de l’agent de protection par mot de passe Azure AD

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité de préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12100"></a>1.2.10.0

Date de publication : 17/08/2018

Correctifs :

* Register-AzureADPasswordProtectionProxy et Register-AzureADPasswordProtectionForest prennent désormais en charge l’authentification multifacteur
* Register-AzureADPasswordProtectionProxy nécessite un contrôleur de domaine WS2012 ou ultérieur dans le domaine afin d’éviter les erreurs de chiffrement.
* Le service de l’agent de contrôleur de domaine est plus fiable en ce qui concerne les demandes de nouvelle stratégie de mot de passe à Azure au démarrage.
* Le service de l’agent de contrôleur de domaine demande une nouvelle stratégie de mot de passe à Azure toutes les heures si nécessaire, mais il le fait désormais à compter d’une heure de début sélectionnée de façon aléatoire.
* Le service de l’agent de contrôleur de domaine ne provoque plus un délai illimité lors d’une nouvelle publication de contrôleur de domaine en cas d’installation sur un serveur avant sa promotion en tant que réplica.
* Le service de l’agent de contrôleur de domaine respecte désormais le paramètre de configuration « Activer la protection par mot de passe sur Windows Server Active Directory ».
* Les programmes d’installation du proxy et de l’agent de contrôleur de domaine prennent désormais en charge la mise à niveau sur place en cas de mise à niveau vers des versions ultérieures.

> [!WARNING]
> La mise à niveau sur place à partir de la version 1.1.10.3 n’est pas prise en charge et provoque une erreur d’installation. Pour mettre à niveau vers la version 1.2.10 ou ultérieure, vous devez d’abord désinstaller complètement le logiciel du service de proxy et d’agent de contrôleur de domaine, puis installer la nouvelle version à partir de zéro. La réinscription du service de proxy de protection par mot de passe Azure AD est nécessaire.  La réinscription de la forêt n’est pas nécessaire.

> [!NOTE]
> Les mises à niveau sur place du logiciel d’agent de contrôleur de domaine nécessitent un redémarrage.

* Le service de proxy et l’agent de contrôleur de domaine prennent désormais en charge l’exécution sur un serveur configuré pour utiliser uniquement des algorithmes compatibles FIPS.
* Amélioration de la journalisation
* Correctifs mineurs de performances et de robustesse

## <a name="11103"></a>1.1.10.3

Date de publication : 15/06/2018

Préversion publique initiale

## <a name="next-steps"></a>Étapes suivantes

[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
