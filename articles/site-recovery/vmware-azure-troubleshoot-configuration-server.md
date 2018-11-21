---
title: Résoudre les problèmes touchant le serveur de configuration durant la reprise d’activité de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit des solutions aux problèmes courants pour permettre le déploiement d’un serveur de configuration durant la reprise d’activité de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: ramamill
ms.openlocfilehash: b819783d127c51c0d5f33b2273a37a4180cb13a6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569736"
---
# <a name="troubleshoot-configuration-server-issues"></a>Résoudre les problèmes de serveur de configuration

Cet article vous aide à résoudre les problèmes lors du déploiement et de la gestion d’un serveur de configuration [Azure Site Recovery](site-recovery-overview.md). Le serveur de configuration est utilisé pour la configuration d’une reprise après sinistre de machines virtuelles VMware et de serveurs physiques sur Azure à l’aide de Site Recovery. 

## <a name="installation-failures"></a>Échecs d’installation

| **Message d’erreur** | **Action recommandée** |
|--------------------------|------------------------|
|ERREUR   Impossible de charger des comptes. Erreur : System.IO.IOException : Impossible de lire les données de la connexion de transport lors de l’installation et de l’inscription du serveur CS.| Vérifiez que TLS 1.0 est activé sur l’ordinateur. |

## <a name="registration-failures"></a>Échecs d’enregistrement

Les échecs d’enregistrement peuvent être débogués à l’aide des journaux dans le dossier %ProgramData%\ASRLogs.

Les échecs d’enregistrement peuvent être débogués en consultant les journaux dans le dossier **%ProgramData%\ASRLogs**.

| **Message d’erreur** | **Action recommandée** |
|--------------------------|------------------------|
|**09:20:06** :InnerException.Type : SrsRestApiClientLib.AcsException,InnerException.<br>Message : ACS50008 : Jeton SAML non valide.<br>ID de suivi : 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de corrélation : 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Timestamp : **2016-12-12 14:50:08Z<br>** | Vérifiez que l’horloge de votre système n’a pas plus de 15 minutes de décalage avec l’heure locale. Réexécutez le programme d’installation pour terminer l’inscription.|
|**09:35:27** : Une exception DRRegistrationException s’est produite en essayant d’obtenir tout l’archivage de récupération d’urgence pour le certificat sélectionné : : Exception levée.Type :Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message : ACS50008 : Jeton SAML non valide.<br>ID de suivi : e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de corrélation : abe9deb8-3e64-464d-8375-36db9816427a<br>Timestamp : **2016-05-19 01:35:39Z**<br> | Vérifiez que l’horloge de votre système n’a pas plus de 15 minutes de décalage avec l’heure locale. Réexécutez le programme d’installation pour terminer l’inscription.|
|06:28:45 : Échec de création du certificat<br>06:28:45 : Impossible de poursuivre le programme d’installation. Impossible de créer le certificat requis pour s’authentifier auprès de Site Recovery. Réexécuter le programme d’installation | Assurez-vous d’exécuter le programme d’installation en tant qu’administrateur local. |
