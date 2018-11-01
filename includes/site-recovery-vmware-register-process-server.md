---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165635"
---
* Connectez-vous à la machine virtuelle du serveur de traitement à l’aide de la connexion Bureau à distance.
* Vous pouvez lancer cspsconfigtool.exe en cliquant sur le raccourci sur le bureau. (L’outil est lancé automatiquement si c’est la première fois que vous vous connectez au serveur de traitement.)
  - Nom de domaine complet ou adresse IP du serveur de configuration
  - Port sur lequel le serveur de configuration écoute. La valeur doit être 443
  - Phrase secrète de connexion pour se connecter au serveur de configuration.
  - Port de transfert de données à configurer pour ce serveur de traitement. Conservez la valeur par défaut, sauf si vous avez opté pour un numéro de port différent dans votre environnement.

    ![Inscrire le serveur de traitement](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Cliquez sur le bouton Enregistrer pour enregistrer la configuration et inscrire le serveur de traitement.
