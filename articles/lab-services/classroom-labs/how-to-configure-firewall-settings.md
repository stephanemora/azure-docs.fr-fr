---
title: Paramètres de pare-feu pour Azure Lab Services
description: Découvrez comment déterminer l’adresse IP publique et la plage de numéros de port pour les machines virtuelles d’un labo de façon à pouvoir ajouter ces informations aux règles de pare-feu.
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692637"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Paramètres de pare-feu pour Azure Lab Services

Chaque organisation ou établissement d’enseignement configure son propre réseau en fonction de ses besoins.  Parfois, cela peut consister à définir des règles de pare-feu qui bloquent les connexions RDP (Remote Desktop Protocol) ou SSH (Secure Shell) vers les ordinateurs situés en dehors du réseau.  Sachant qu’Azure Lab Services s’exécute dans le cloud public, il peut être nécessaire de procéder à une configuration supplémentaire pour permettre aux étudiants d’accéder à leur machine virtuelle en se connectant à partir du réseau du campus.

Chaque labo utilise une adresse IP publique unique et plusieurs ports.  Toutes les machines virtuelles, que ce soit le modèle de machine virtuelle ou les machines virtuelles des étudiants, sont appelées à utiliser cette adresse IP publique.  L’adresse IP publique ne changera pas pendant la durée du labo.  En revanche, chaque machine virtuelle aura un numéro de port différent.  Les numéros de port sont compris entre 49152 et 65535.  L’adresse IP publique et le numéro de port sont utilisés conjointement pour connecter le formateur et les étudiants à la machine virtuelle appropriée.  Cet article explique notamment comment trouver l’adresse IP publique utilisée par un labo.  Ces informations s’avèrent utiles pour mettre à jour les règles de pare-feu entrantes et sortantes et ainsi permettre aux étudiants d’accéder à leur machine virtuelle.

>[!IMPORTANT]
>Chaque labo aura une adresse IP publique différente.

## <a name="find-public-ip-for-a-lab"></a>Trouver l’adresse IP publique d’un labo

L’adresse IP publique de chaque labo figure dans le panneau **Tous les labs** du compte Lab Services.  Pour savoir comment trouver le panneau **Tous les labs**, suivez les instructions dans [Gérer les labos dans un compte Lab](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Panneau Tous les labs](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Vous ne verrez pas l’adresse IP publique si le modèle d’ordinateur de votre labo n’a pas encore été publié.

## <a name="conclusion"></a>Conclusion

Nous connaissons maintenant l’adresse IP publique du labo.  Des règles trafic entrant et sortant peuvent être créées pour le pare-feu de l’organisation, l’adresse IP publique et la plage de ports 49152-65535.  Une fois les règles mises à jour, les étudiants peuvent accéder à leur machine virtuelle sans que le pare-feu réseau bloque l’accès.
