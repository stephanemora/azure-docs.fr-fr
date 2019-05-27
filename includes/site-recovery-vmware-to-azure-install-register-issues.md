---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127715"
---
### <a name="installation-failures"></a>Échecs d’installation
| **Exemple de message d’erreur** | **Action recommandée** |
|--------------------------|------------------------|
|ERREUR   Impossible de charger des comptes. Error: System.IO.IOException : Impossible de lire les données de la connexion de transport lors de l’installation et de l’inscription du serveur CS.| Vérifiez que TLS 1.0 est activé sur l’ordinateur. |

### <a name="registration-failures"></a>Échecs d’enregistrement
Les échecs d’enregistrement peuvent être débogués en consultant les journaux d’activité dans le dossier **%ProgramData%\ASRLogs**.

| **Exemple de message d’erreur** | **Action recommandée** |
|--------------------------|------------------------|
|**09:20:06** :InnerException.Type : SrsRestApiClientLib.AcsException,InnerException.<br>Message : ACS50008 : Le jeton SAML n’est pas valide.<br>ID de suivi : 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de corrélation : 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Horodatage : **2016-12-12 14:50:08Z<br>** | Vérifiez que l’horloge de votre système n’a pas plus de 15 minutes de décalage avec l’heure locale. Réexécutez le programme d’installation pour terminer l’inscription.|
|**09:35:27** : Une exception DRRegistrationException s’est produite en essayant d’obtenir tout l’archivage de reprise d’activité pour le certificat sélectionné : Exception Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException levée, Message d’exception : ACS50008 : Le jeton SAML n’est pas valide.<br>ID de suivi : e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de corrélation : abe9deb8-3e64-464d-8375-36db9816427a<br>Horodatage : **2016-05-19 01:35:39Z**<br> | Vérifiez que l’horloge de votre système n’a pas plus de 15 minutes de décalage avec l’heure locale. Réexécutez le programme d’installation pour terminer l’inscription.|
|06:28:45 : Échec de création du certificat<br>06:28:45 : Impossible de poursuivre le programme d’installation. Impossible de créer le certificat requis pour s’authentifier auprès de Site Recovery. Réexécuter le programme d’installation | Assurez-vous d’exécuter le programme d’installation en tant qu’administrateur local. |
