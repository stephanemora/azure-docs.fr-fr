---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: cd18d71d26410767a2d3119c12a1339bdc84bd33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116119"
---
1. Sur votre serveur de configuration, lancez l’outil CSPSConfigtool.exe. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier *emplacement d’installation*\home\svsystems\bin.
2. Cliquez sur **Gérer les comptes** > **Ajouter un compte**.

    ![Ajouter un compte](./media/site-recovery-add-vcenter-account/credentials1.png)
3. Dans **Détails du compte**, ajoutez le compte qui sera utilisé pour la découverte automatique.

    ![Détails](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
   > L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, cliquez sur **Serveurs de configuration** > ***nom du serveur*** > **Actualiser le serveur**.
