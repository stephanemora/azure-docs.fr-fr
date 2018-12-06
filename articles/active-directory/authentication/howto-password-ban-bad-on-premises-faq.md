---
title: FAQ sur la protection par mot de passe Azure AD en local
description: FAQ sur la protection par mot de passe Azure AD en local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: d3d42a3c81153d54690f0825368eaa950dbac18e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314777"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Préversion : protection par mot de passe Azure AD en local - Forum aux questions

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité de préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Questions générales

**Q : Quand la fonctionnalité de protection par mot de passe Azure AD sera-t-elle en disponibilité générale (GA) ?**

Nous n’avons pas encore annoncé de date de disponibilité générale.

**Q : La protection par mot de passe Azure AD en local est-elle prise en charge sur les clouds non publics ?**

Non. La protection par mot de passe Azure AD en local est prise en charge uniquement sur le cloud public.

**Q : Comment puis-je appliquer des avantages de la protection par mot de passe Azure AD à un sous-ensemble de mes utilisateurs en local ?**

Non pris en charge. Une fois déployée et activée, la protection par mot de passe Azure AD ne fait pas de distinction : tous les utilisateurs reçoivent les mêmes avantages en matière de protection.

**Q : L’installation de la protection par mot de passe Azure AD simultanément avec d’autres produits basés sur le filtrage par mot de passe est-elle prise en charge ?**

Oui. La prise en charge de plusieurs DLL de filtrage de mots de passe enregistrés est une fonctionnalité Windows de base et n’est pas spécifique à la protection par mot de passe Azure AD. Toutes les DLL de filtrage de mots de passe enregistrés doivent valider un mot de passe avant de l’accepter.

**Q : Pourquoi la technologie DFSR est-elle nécessaire pour la réplication sysvol ?**

La technologie FRS (prédécesseur de la technologie DFSR) présente de nombreux problèmes connus et n’est pas du tout prise en charge par les versions plus récentes de Windows Server Active Directory. Aucun essai de la protection par mot de passe Azure AD ne sera effectué sur les domaines configurés en FRS.

Pour plus d’informations, consultez les articles suivants :

[Argumentaire en faveur de la migration de la réplication sysvol vers DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Q : Pourquoi un redémarrage est-il nécessaire pour installer ou mettre à niveau le logiciel de l’agent contrôleur de domaine ?**

Cette exigence est due à un comportement de base de Windows.

**Q : Existe-t-il un moyen de configurer un agent contrôleur de domaine pour utiliser un serveur proxy spécifique ?**

 Non.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez une question sur la protection par mot de passe Azure AD en local restée sans réponse ici, adressez-nous votre question en soumettant votre commentaire ci-dessous. Merci !

[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
