---
title: Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution
description: Découvrez comment effectuer la rotation des informations d’identification de vCenter Server et de NSX-T Manager pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 6e7a37a1c262548e49952ed162cf725b1a7d6ed1
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789747"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution

Cet article vous guide tout au long de la procédure de rotation des informations d’identification cloudadmin pour votre cloud privé Azure VMware Solution.  Bien que vos informations d’identification d’administrateur vCenter Server CloudAdmin et NSX-T Manager n’expirent pas, vous pouvez générer de nouveaux mots de passe pour ces comptes. Après avoir effectué la roation des informations d’identification, vérifiez que le connecteur HCX dispose des informations d’identification vCenter Server les plus récentes.

Vous pouvez également regarder une vidéo sur la façon de [réinitialiser le mot de passe d’administrateur vCenter CloudAdminet et NSX-T](https://youtu.be/cK1qY3knj88). 

## <a name="prerequisites"></a>Prérequis

Si vous utilisez vos informations d’identification cloudadmin pour des services connectés tels que HCX, vRealize Orchestrator, vRealize Operations Manager ou VMware Horizon, vos connexions cessent de fonctionner une fois que vous avez mis à jour votre mot de passe.  Arrêtez ces services avant d’effectuer la rotation du mot de passe. Sinon, vous constaterez des verrous temporaires sur vos comptes d’administrateur NSX-T et CloudAdmin vCenter, car ces services appellent en permanence à l’aide de vos anciennes informations d’identification.  Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](./concepts-identity.md).

## <a name="reset-your-azure-vmware-solution-credentials"></a>Réinitialiser les informations d’identification Azure VMware Solution

Dans cette étape, vous allez effectuer la rotation des informations d’identification cloudadmin pour vos composants Azure VMware Solution. 

>[!NOTE]
>N’oubliez pas de remplacer **{SubscriptionID}** , **{ResourceGroup}** et **{PrivateCloudName}** par les informations de votre cloud privé.

1. À partir du portail Azure, ouvrez une session Azure Cloud Shell.

2. Mettez à jour votre mot de passe vCenter CloudAdmin.  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. Mettez à jour votre mot de passe d’administrateur NSX-T. 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="verify-hcx-connector-has-the-latest-credentials"></a>Vérifier que le connecteur HCX dispose des informations d’identification les plus récentes

Dans cette étape, vous allez vérifier que le connecteur HCX dispose des informations d’identification mises à jour.

1. Accédez au connecteur HCX local à l’adresse à l’adresse https://{ip of the HCX connector appliance}:443 et connectez-vous à l’aide des nouvelles informations d’identification.

   Veillez à utiliser le port 443. 

2. Dans le tableau de bord VMware HCX, sélectionnez **Site Pairing (Jumelage de sites)** .
    
   :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Capture d’écran du tableau de bord VMware HCX avec l’option Site Pairing mise en surbrillance.":::
 
3. Sélectionnez la connexion à Azure VMware Solution qui convient, puis **Modifier la connexion**.
 
4. Fournissez les nouvelles informations d’identification utilisateur de CloudAdmin vCenter Server, puis sélectionnez **Modifier** pour les enregistrer. L’enregistrement doit s’afficher correctement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment réinitialiser les informations d’identification de vCenter Server et de NSX-T Manager pour Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Configurer des composants réseau NSX dans Azure VMware Solution](configure-nsx-network-components-azure-portal.md)
- [Surveiller et gérer des machines virtuelles Azure VMware Solution](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Déployer une récupération d’urgence de machines virtuelles à l’aide d’Azure VMware Solution](disaster-recovery-for-virtual-machines.md)
