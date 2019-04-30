---
title: 'Azure AD Connect : Résoudre les problèmes d’ancre Source pendant l’Installation | Microsoft Docs'
description: Cette rubrique explique comment résoudre les problèmes liés à l’ancre source pendant l’installation.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114152"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Résolution des problèmes d’ancre Source pendant l’Installation
Cet article explique les différents problèmes qui peuvent se produire pendant l’installation et des offres moyens de résoudre ces problèmes liés à l’ancre source.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ancre Source non valide dans Azure Active Directory

### <a name="custom-installation"></a>Installation personnalisée

Pendant l’installation personnalisée, Azure AD Connect lit la stratégie d’ancre source dans Azure Active Directory. Si la stratégie existe dans Azure Active Directory, Azure AD Connect applique la stratégie de même, sauf si elle est substituée par le client. L’Assistant vous indique quel attribut a été lu. En outre, l’Assistant vous avertit si vous essayez de remplacer la stratégie d’ancre source.

Au cours de cette opération de lecture, il est possible que la stratégie d’ancre source dans Azure Active Directory est inattendue. Dans ce cas, Azure AD Connect ne sait pas quel l’ancre source à utiliser et a besoin de remplacement manuel.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Pour résoudre ce problème, vous pouvez substituer manuellement l’ancre source en sélectionnant un attribut spécifique. Continuer avec cette option uniquement si vous êtes certain de l’attribut à sélectionner. Si vous n’êtes pas certain, contactez [prise en charge Microsoft](https://support.microsoft.com/contactus/) pour obtenir des conseils. Si vous modifiez la stratégie d’ancre source, il peut rompre l’association entre vos utilisateurs locaux et leurs ressources Azure associées.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Installation rapide
Pendant l’installation expresse, Azure AD Connect lit la stratégie d’ancre source dans Azure Active Directory. Si la stratégie existe dans Azure Active Directory, Azure AD Connect applique la même stratégie. Il n’existe aucune option pour effectuer le remplacement manuel.

Au cours de cette opération de lecture, il est possible que la stratégie d’ancre source dans Azure Active Directory est inattendue. Dans ce cas, Azure AD Connect ne sait pas quelle doit être l’ancre source.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Pour résoudre ce problème, vous devez réinstaller le programme en utilisant le mode personnalisé et de remplacer manuellement l’ancre source en sélectionnant un attribut spécifique. Continuer avec cette option uniquement si vous êtes certain de l’attribut à sélectionner. Si vous n’êtes pas certain, contactez [prise en charge Microsoft](https://support.microsoft.com/contactus/) pour obtenir des conseils. Si vous modifiez la stratégie d’ancre source, il peut rompre l’association entre vos utilisateurs locaux et leurs ressources Azure associées.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ancre Source non valide dans le moteur de synchronisation
Pendant l’installation, il est possible de Azure AD se connecter tente de configurer le moteur de synchronisation à l’aide d’une ancre source non valide. Cette opération est très probablement un problème de produit et l’installation d’Azure AD Connect échoue. Contact [prise en charge Microsoft](https://support.microsoft.com/contactus/) si vous exécutez dans à ce problème.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).