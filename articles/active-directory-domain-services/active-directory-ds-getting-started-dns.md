---
title: 'Azure Active Directory Domain Services : Mettre à jour les paramètres DNS pour le réseau virtuel Azure | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: iainfou
ms.openlocfilehash: ef0c25227e98d066d75c9adb62af3f602e35db23
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474156"
---
# <a name="enable-azure-active-directory-domain-services"></a>Activation d’Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : Mettre à jour les paramètres DNS pour le réseau virtuel Azure
Dans les tâches de configuration précédentes, vous avez activé Azure Active Directory Domain Services pour votre répertoire. Ensuite, permettez aux ordinateurs du réseau virtuel de se connecter et d’utiliser ces services. Dans cet article, vous mettez à jour les paramètres de serveur DNS de votre réseau virtuel afin qu’il pointe vers les deux adresses IP pour lesquelles Azure Active Directory Domain Services est disponible sur le réseau virtuel.

Afin de mettre à jour les paramètres de serveur DNS pour le réseau virtuel sur lequel vous avez activé Azure Active Directory Domain Services, procédez comme suit :


1. L’onglet **Vue d’ensemble** répertorie un ensemble d’**étapes de configuration requises** à effectuer une fois que votre domaine managé est entièrement configuré. La première étape de configuration est **Mettre à jour les paramètres du serveur de noms de domaine pour votre réseau virtuel**.

    ![Services de domaine - Onglet Vue d’ensemble](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Vous ne voyez pas cette étape de configuration ? Si les paramètres du serveur DNS pour votre réseau virtuel sont à jour, vous ne verrez pas la vignette « Mettre à jour les paramètres du serveur de noms de domaine pour votre réseau virtuel » sous l’onglet Vue d’ensemble.
    >
    >

2. Cliquez sur le bouton **Configurer** pour mettre à jour les paramètres de serveur DNS pour le réseau virtuel.

> [!NOTE]
> Les machines virtuelles du réseau n’obtiendront les nouveaux paramètres DNS qu’après un redémarrage. Si vous avez besoin qu’elles obtiennent les paramètres DNS mis à jour immédiatement, déclenchez un redémarrage via le portail, PowerShell ou l’interface CLI.
>
>

## <a name="next-step"></a>Étape suivante
[Tâche 5 : activer la synchronisation de hachage de mot de passe pour Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
