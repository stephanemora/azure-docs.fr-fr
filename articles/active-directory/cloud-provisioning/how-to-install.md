---
title: Installation de l’agent de provisionnement cloud Azure AD Connect
description: Cette rubrique décrit étape par étape l’installation de l’agent de provisionnement.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846230"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Installer l’agent de provisionnement cloud Azure AD Connect
Ce document vous guide tout au long du processus d’installation de l’agent de provisionnement Azure AD Connect, et explique comment le configurer au départ dans le portail Azure.

>[!IMPORTANT]
>Les instructions d’installation suivantes supposent que tous les [prérequis](how-to-prerequisites.md) ont été réunis.

L’installation et la configuration du provisionnement Azure AD Connect sont accomplies dans les étapes suivantes :
    
- [Installer l’agent](#install-the-agent)
- [Vérifier l’installation de l’agent](#verify-agent-installation)


## <a name="install-the-agent"></a>Installer l’agent

1. Connectez-vous au serveur que vous allez utiliser à l’aide des autorisations d’administrateur d’entreprise.
2. Accédez au portail Azure et sélectionnez Azure Active Directory sur la gauche.
3. Cliquez sur **Gérer le provisionnement (préversion)** et sélectionnez **Passer en revue tous les agents**.
3. Téléchargez l’agent de provisionnement Azure AD Connect à partir du portail Azure.
![Écran d’accueil](media/how-to-install/install9.png)</br>
3. Exécuter le provisionnement Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. Dans l’écran de démarrage, **acceptez** les termes du contrat de licence et cliquez sur **Installer**.</br>
![Écran d’accueil](media/how-to-install/install1.png)</br>

4. Une fois cette opération terminée, l’Assistant de configuration démarre.  Connectez-vous avec votre compte d’administrateur général Azure AD.
5. Dans l’écran **Connexion à Active Directory**, cliquez sur **Ajout d’un annuaire**, puis connectez-vous avec votre compte d’administrateur Active Directory.  Cette opération ajoute votre annuaire local.  Cliquez sur **Suivant**.</br>
![Écran d’accueil](media/how-to-install/install3.png)</br>

6. Dans l’écran **Configuration terminée**, cliquez sur **Confirmer**.  Cette opération inscrit et redémarre l’agent.</br>
![Écran d’accueil](media/how-to-install/install4.png)</br>

7. Une fois cette opération terminée, vous devriez voir une notification : **La configuration de votre agent a été vérifiée avec succès.**  Vous pouvez cliquer sur **Quitter**.</br>
![Écran d’accueil](media/how-to-install/install5.png)</br>
8. Si vous voyez encore l’écran de démarrage initial, cliquez sur **Fermer**.


## <a name="verify-agent-installation"></a>Vérifier l’installation de l’agent
La vérification de l’agent se produit dans le portail Azure et sur le serveur local qui exécute l’agent.

### <a name="azure-portal-agent-verification"></a>Vérification de l’agent dans le portail Azure
Pour vérifier que l’agent est visible par Azure, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Sur la gauche, sélectionnez **Azure Active Directory**, cliquez sur **Azure AD Connect** et, au centre, sélectionnez **Gérer le provisionnement (préversion)** .</br>
![Portail Azure](media/how-to-install/install6.png)</br>

3.  Dans l’écran **Provisionnement Azure AD (préversion)** , cliquez sur **Passer en revue tous les agents**.
![Provisionnement Azure AD](media/how-to-install/install7.png)</br>
 
4. Dans l’écran **Agents de provisionnement locaux**, vous voyez les agents que vous avez installés.  Vérifiez que l’agent en question est présent et qu’il est marqué comme étant **actif**.
![Agents de provisionnement](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Vérifier le port
Pour vérifier que le service Azure est à l’écoute sur le port 443, et que votre agent peut communiquer avec lui, vous pouvez utiliser l’outil suivant :

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ce test permet de vérifier que vos agents sont en mesure de communiquer avec Azure via le port 443.  Ouvrez un navigateur et accédez à l’URL ci-dessus à partir du serveur sur lequel l’agent est installé.
![Services](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Sur le serveur local
Pour vérifier que l’agent est en cours d’exécution, effectuez les étapes suivantes :

1.  Connectez-vous au serveur avec un compte administrateur.
2.  Ouvrez **Services** en y accédant ou en accédant à Démarrer/Exécuter/Services.msc.
3.  Sous **Services**, assurez-vous que le **Programme de mise à jour de l’agent Microsoft Azure AD Connect** et l’**Agent de provisionnement Microsoft Azure AD Connect** sont présents, et que leur état est **En cours d’exécution**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>L’agent a été installé, mais il doit être configuré et activé pour pouvoir démarrer la synchronisation des utilisateurs.  Pour configurer un nouvel agent, consultez [Configuration d’un nouvel agent de provisionnement Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)
 
