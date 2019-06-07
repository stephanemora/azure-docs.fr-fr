---
title: Validation contrôlée d’une jointure hybrid Azure AD - Azure AD
description: Apprenez à effectuer une validation contrôlée de jointure Azure AD hybride avant de l’activer dans toute l’organisation à la fois
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513284"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validation contrôlée de la jonction Azure AD hybride

Lorsque toutes les conditions préalables sont en place, les appareils Windows seront inscriront automatiquement en tant qu’appareils dans votre locataire Azure AD. L’état de ces identités des appareils dans Azure AD est appelée une jointure hybrid Azure AD. Vous trouverez plus d’informations sur les concepts abordés dans cet article dans les articles [Introduction à la gestion des appareils dans Azure Active Directory](overview.md) et [planifier votre implémentation de jonction hybride Azure Active Directory ](hybrid-azuread-join-plan.md).

Organisations peuvent vouloir effectuer une validation contrôlée de jointure Azure AD hybride avant de l’activer dans leur organisation entière à la fois. Cet article explique comment effectuer une validation contrôlée de jointure Azure AD hybride.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validation contrôlée d’une jointure hybrid Azure AD sur les appareils Windows actuels

Pour les appareils qui fonctionnent avec le système d’exploitation d’ordinateur Windows, la version prise en charge est la Mise à jour anniversaire Windows 10 (version 1607) ou une version ultérieure. La meilleure pratique consiste à effectuer une mise à niveau vers la dernière version de Windows 10.

Pour effectuer une validation contrôlée d’une jointure hybrid Azure AD sur les appareils Windows actuels, vous devez :

1. Effacer l’entrée de Point de connexion de Service (SCP) à partir d’Active Directory (AD) si elle existe
1. Configurer le paramètre de Registre côté client pour SCP sur vos ordinateurs joints au domaine à l’aide d’un objet de stratégie de groupe (GPO)
1. Si vous utilisez AD FS, vous devez également configurer le paramètre de Registre côté client pour SCP sur votre serveur AD FS à l’aide d’un objet de stratégie de groupe  



### <a name="clear-the-scp-from-ad"></a>Désactivez le SCP à partir d’AD

Utilisez l’éditeur Active Directory Services Interfaces (ADSI Edit) pour modifier les objets SCP dans Active Directory.

1. Lancer le **ADSI Edit** à partir d’application de bureau et station de travail d’administration ou un contrôleur de domaine en tant qu’administrateur d’entreprise.
1. Se connecter à la **le contexte de nommage de Configuration** de votre domaine.
1. Accédez à **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. Cliquez avec le bouton droit sur l’objet modèle feuille sous **CN = Device Registration Configuration** et sélectionnez **propriétés**
   1. Sélectionnez **mots clés** à partir de la **Éditeur d’attributs** fenêtre et cliquez sur **modifier**
   1. Sélectionnez les valeurs de **azureADId** et **azureADName** (un à la fois) et cliquez sur **supprimer**
1. Fermer **éditeur ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurer le paramètre de Registre côté client pour SCP

L’exemple suivant permet de créer un objet de stratégie de groupe (GPO) pour déployer un paramètre de Registre configuration d’une entrée de SCP dans le Registre de vos appareils.

1. Ouvrez une console de gestion des stratégies de groupe et créer un nouvel objet de stratégie de groupe dans votre domaine.
   1. Nommez votre objet de stratégie de groupe nouvellement créé (par exemple, ClientSideSCP).
1. Modifier l’objet de stratégie de groupe et recherchez le chemin suivant : **Configuration de l’ordinateur** > **préférences** > **Windows paramètres** > **Registre**
1. Avec le bouton droit sur le Registre, puis sélectionnez **New** > **élément de Registre**
   1. Sur le **général** , configurez les éléments suivants
      1. Action : **Mettre à jour**
      1. Hive : **HKEY_LOCAL_MACHINE**
      1. Chemin de clé : **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nom de la valeur : **TenantId**
      1. Type de valeur : **REG_SZ**
      1. Données de valeur : Le GUID ou **ID de répertoire** de votre instance Azure AD (cette valeur se trouve dans le **Azure portal** > **Azure Active Directory**  >   **Propriétés** > **ID de répertoire**)
   1. Cliquez sur **OK**
1. Avec le bouton droit sur le Registre, puis sélectionnez **New** > **élément de Registre**
   1. Sur le **général** , configurez les éléments suivants
      1. Action : **Mettre à jour**
      1. Hive : **HKEY_LOCAL_MACHINE**
      1. Chemin de clé : **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nom de la valeur : **TenantName**
      1. Type de valeur : **REG_SZ**
      1. Données de valeur : Votre vérifié **nom de domaine** dans Azure AD (par exemple, `contoso.onmicrosoft.com` ou tout autre nom de domaine vérifié dans votre répertoire)
   1. Cliquez sur **OK**
1. Fermez l’éditeur pour l’objet de stratégie de groupe nouvellement créé
1. Lier le GPO nouvellement créé à l’unité d’organisation souhaitée contenant des ordinateurs joints au domaine qui appartiennent à votre population de lancement contrôlé

### <a name="configure-ad-fs-settings"></a>Configurer les paramètres AD FS

Si vous utilisez AD FS, vous devez d’abord configurer le SCP côté client en suivant les instructions mentionnées ci-dessus, mais la liaison de l’objet de stratégie de groupe à vos serveurs AD FS. Cette configuration est nécessaire pour AD FS établir la source pour les identités des appareils comme Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validation contrôlée d’une jointure hybrid Azure AD sur les appareils de bas niveau Windows

Pour inscrire des appareils de bas niveau Windows, les organisations doivent installer [Microsoft Workplace Join pour les ordinateurs non-Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponible sur du Microsoft Download Center.

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels comme [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Le package prend en charge les options d’installation sans assistance standard avec le paramètre quiet. La branche actuelle de Configuration Manager offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions effectuées.

Le programme d’installation crée une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche joint en mode silencieux l’appareil à Azure AD, au moyen des informations d’identification de l’utilisateur, après son authentification auprès d’Azure AD.

Pour contrôler l’inscription d’appareil, vous devez déployer le package Windows Installer pour votre groupe d’appareils de bas niveau Windows sélectionné.

> [!NOTE]
> Si un SCP n’est pas configuré dans Active Directory, vous devez suivre la même approche décrite pour [configurer le paramètre de Registre côté client pour SCP](#configure-client-side-registry-setting-for-scp)) sur vos ordinateurs joints au domaine à l’aide d’un objet de stratégie de groupe (GPO).


Après avoir vérifié que tout fonctionne comme prévu, vous pouvez inscrire automatiquement le reste de vos appareils en cours et de bas niveau de Windows avec Azure AD par [configuration SCP à l’aide d’Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Étapes suivantes

[Planifier l’implémentation de la jonction Azure Active Directory hybride](hybrid-azuread-join-plan.md)
