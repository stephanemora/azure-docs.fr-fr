---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177592"
---
Dans **Ajouter un serveur vCenter** , spécifiez un nom convivial pour l’hôte vSphere ou le serveur vCenter, puis indiquez l’adresse IP ou le nom de domaine complet public du serveur. Conservez le port 443, sauf si vos serveurs VMware sont configurés pour écouter les demandes sur un port différent. Sélectionnez le compte à connecter au serveur VMware vCenter ou vSphere ESXi. Cliquez sur **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Si vous ajoutez le serveur VMware vCenter ou un hôte VMware vSphere à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou hôte, assurez-vous que le compte bénéficie des autorisations suivantes : centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle et commutateur distribué vSphere. En outre, le serveur VMware vCenter exige que le privilège d’affichage de stockage soit activé.
