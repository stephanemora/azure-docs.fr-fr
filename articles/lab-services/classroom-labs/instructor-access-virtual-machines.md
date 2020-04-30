---
title: Formateur accédant à des machines virtuelles dans Azure Lab Services
description: Cet article explique comment les formateurs peuvent accéder aux machines virtuelles des étudiants depuis la vue du formateur. Par exemple, un assistant d'enseignement peut être formateur pour une classe, mais étudiant pour d’autres classes.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642602"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Accéder aux machines virtuelles en tant qu’étudiant depuis la vue du formateur
Cet article explique aux formateurs comment accéder aux machines virtuelles pour les classes auxquelles ils participent en tant qu'étudiants. 

Voici un scénario illustrant l'utilité de cette fonctionnalité. Un assistant d’enseignement est formateur pour un classe, mais étudiant pour d'autres classes. Et le formateur souhaite afficher les machines virtuelles des étudiants et y accéder depuis la vue du formateur montrant les labs dont il est propriétaire. 

## <a name="access-vms-from-instructor-view"></a>Accéder aux machines virtuelles depuis la vue du formateur

1. Connectez-vous au site web d’[Azure Lab Services](https://labs.azure.com). Vous voyez les labs dont vous êtes propriétaire. Ces labs peuvent être des labs que vous avez créés vous-même ou des labs que l’administrateur vous a affectés en tant que propriétaire. Pour plus d'informations, consultez [Guide pratique pour ajouter des propriétaires à un labo existant](how-to-add-user-lab-owner.md).
2. Pour accéder aux machines virtuelles pour les classes auxquelles vous participez en tant qu’étudiant, sélectionnez l’icône d'ordinateur dans l’angle supérieur droit. Assurez-vous de voir les machines virtuelles auxquelles vous pouvez accéder en tant qu’étudiant. Dans l’exemple suivant, l’utilisateur est assistant d'enseignement du lab Python, mais étudiant du lab Java. L’utilisateur voit donc la machine virtuelle du lab Java dans la liste déroulante. L’utilisateur peut démarrer la machine virtuelle et s’y connecter. 
    
    ![Accéder aux machines virtuelles des étudiants](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Se connecter à une machine virtuelle](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Se connecter à une machine virtuelle avec RDP sur un Mac](connect-virtual-machine-mac-rdp.md)
- [Utiliser le Bureau à distance pour les machines virtuelles Linux](how-to-use-remote-desktop-linux-student.md)
