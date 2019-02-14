---
title: Contrôler la jonction Azure AD Hybride de vos appareils | Microsoft Docs
description: Découvrez comment contrôler la jonction Azure AD Hybride de vos appareils dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b1e1c103c37874365f7e8d0b893985c9a6469c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171075"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Contrôler la jointure d’Azure AD hybride de vos appareils

La jonction Azure AD Hybride (Azure AD) est un processus qui consiste à inscrire automatiquement auprès d’Azure AD des appareils joints au domaine local. Vous pouvez parfois souhaiter ne pas inscrire tous vos appareils automatiquement. C’est le cas, par exemple, lors du déploiement initial, afin de vérifier que tout fonctionne conformément à ce qui est attendu.

Cet article fournit des conseils sur la façon de contrôler la jonction Azure AD Hybride de vos appareils. 


## <a name="prerequisites"></a>Prérequis

Cet article suppose de connaître :

-  [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md)
 
-  [Planifier l’implémentation de la jonction Azure Active Directory Hybride](hybrid-azuread-join-plan.md)

-  [Configurer la jonction Azure Active Directory Hybride pour les domaines managés](hybrid-azuread-join-managed-domains.md) ou [Configurer la jonction Azure Active Directory Hybride pour les domaines fédérés](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Contrôler les appareils Windows actuels

Pour les appareils qui fonctionnent avec le système d’exploitation d’ordinateur Windows, la version prise en charge est la Mise à jour anniversaire Windows 10 (version 1607) ou une version ultérieure. La meilleure pratique consiste à effectuer une mise à niveau vers la dernière version de Windows 10.

Tous les appareils Windows actuels s’inscrivent automatiquement auprès d’Azure AD lorsqu’ils sont démarrés ou que l’utilisateur se connecte. Vous pouvez contrôler ce comportement au moyen d’un objet de stratégie de groupe (GPO) ou par le biais de System Center Configuration Manager.

Pour contrôler les appareils Windows actuels, vous devez : 


1.  **Pour tous les appareils** : désactiver l’inscription automatique d’appareils.
2.  **Pour les appareils sélectionnés** : activer l’inscription automatique d’appareils.

Après avoir vérifié que tout fonctionne conformément à ce qui est attendu, vous êtes prêt à activer de nouveau l’inscription automatique pour tous les appareils.



### <a name="group-policy-object"></a>Objet de stratégie de groupe 

Vous pouvez contrôler le comportement de l’inscription de vos appareils en déployant l’objet de stratégie de groupe suivant : **Enregistrer les ordinateurs appartenant au domaine en tant qu’appareils**.

Pour définir l’objet de stratégie de groupe :

1.  Ouvrez le **Gestionnaire de serveur** et accédez à **Outils** > **Gestion des stratégies de groupe**.

2.  Accédez au nœud de domaine qui correspond au domaine dans lequel vous souhaitez désactiver ou activer l’inscription automatique.

3.  Cliquez avec le bouton droit sur **Objets de stratégie de groupe**, puis sélectionnez **Nouveau**.

4.  Entrez un nom (par exemple, **Jonction Azure AD Hybride**) pour votre objet de stratégie de groupe. 

5.  Sélectionnez **OK**.

6.  Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.

7.  Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Enregistrement d’appareil**. 

8.  Cliquez avec le bouton droit sur **Enregistrer les ordinateurs appartenant à un domaine en tant qu’appareils**, puis sélectionnez **Modifier**.

    > [!NOTE] 
    > Ce modèle de stratégie de groupe a été renommé par rapport aux versions précédentes de la Console de gestion des stratégies de groupe. Si vous utilisez une version antérieure de la console, accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Workplace Join** > **Joindre automatiquement les ordinateurs clients à l’espace de travail**. 

9.  Sélectionnez un des paramètres suivants, puis sélectionnez **Appliquer** :

    - **Désactivé** : pour éviter l’inscription automatique d’appareils.
    - **Enabled** : pour activer l’inscription automatique d’appareils.

10. Sélectionnez **OK**.

Vous devez lier l’objet de stratégie de groupe à l’emplacement de votre choix. Par exemple, pour définir cette stratégie pour tous les appareils actuels joints au domaine dans votre organisation, liez l’objet de stratégie de groupe au domaine. Pour effectuer un déploiement contrôlé, définissez cette stratégie sur les appareils Windows actuels joints au domaine qui appartiennent à une unité d’organisation ou à un groupe de sécurité.

### <a name="configuration-manager-controlled-deployment"></a>Déploiement contrôlé du Gestionnaire de configuration 

Vous pouvez contrôler le comportement d’inscription de vos appareils actuels en configurant le paramètre client suivant : **Inscrire automatiquement les nouveaux appareils joints au domaine Windows 10 auprès d’Azure Active Directory**.

Pour configurer le paramètre client :

1.  Ouvrez **Gestionnaire de configuration**, puis accédez à **Services Cloud**.

2.  Sous **Paramètres de l’appareil**, sélectionnez un des paramètres suivants pour **Inscrire automatiquement les nouveaux appareils joints au domaine Windows 10 auprès d’Azure Active Directory** :

    - **Non** : pour éviter l’inscription automatique d’appareils.
    - **Oui** : pour activer l’inscription automatique d’appareils.


3.  Sélectionnez **OK**.
    

Vous devez lier ce paramètre client à l’emplacement de votre choix. Par exemple, pour configurer ce paramètre client pour tous les appareils Windows actuels dans votre organisation, liez le paramètre client au domaine. Pour effectuer un déploiement contrôlé, vous pouvez configurer le paramètre client pour les appareils Windows actuels joints au domaine qui appartiennent à une unité d’organisation ou à un groupe de sécurité.

> [!Important]
> Bien que la précédente configuration prenne en charge les appareils Windows 10 joints à un domaine existant, des appareils joints récemment au domaine peuvent encore tenter d’effectuer une jonction Azure AD Hybride, et ce en raison du délai pouvant exister dans l’application des paramètres de la stratégie de groupe ou de Configuration Manager sur les appareils. 
>
> Pour éviter ce problème, nous recommandons de créer une image Sysprep (utilisée comme exemple pour une méthode de provisionnement). Créez-la à partir d’un appareil qui n’a encore jamais été joint à Azure AD Hybride, et dont le paramètre de stratégie de groupe ou le paramètre de client Configuration Manager est déjà appliqué. Vous devez également utiliser la nouvelle image pour le provisionnement des nouveaux ordinateurs qui joignent le domaine de votre organisation. 

## <a name="control-windows-down-level-devices"></a>Contrôler des appareils Windows de bas niveau

Pour inscrire des appareils Windows de bas niveau, vous devez télécharger et installer le package Windows Installer (.msi) à partir du Centre de téléchargement, dans la page [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) (Microsoft Workplace Join pour les ordinateurs non Windows 10).

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels, comme [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Le package prend en charge les options d’installation sans assistance standard avec le paramètre quiet. La branche actuelle de Configuration Manager offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions effectuées.

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche joint en mode silencieux l’appareil à Azure AD, au moyen des informations d’identification de l’utilisateur, après son authentification auprès d’Azure AD.

Pour contrôler l’inscription des appareils, vous devez déployer le package Windows Installer uniquement sur un groupe sélectionné d’appareils Windows de bas niveau. Si vous avez vérifié que tout fonctionne conformément à ce qui est attendu, vous êtes prêt pour le déploiement du package sur tous les appareils de bas niveau.


## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md)



