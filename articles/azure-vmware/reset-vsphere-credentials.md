---
title: Réinitialiser les informations d’identification vSphere pour Azure VMware Solution
description: Découvrez comment réinitialiser les informations d’identification vSphere pour votre cloud privé Azure VMware Solution et vérifier que le connecteur HCX dispose des informations d’identification vSphere les plus récentes.
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 3533de03cbea0014a8a50cd0103f96273f0680a7
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145300"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Réinitialiser les informations d’identification vSphere pour Azure VMware Solution

Cet article décrit les étapes de réinitialisation des informations d’identification de vCenter Server et de NSX-T Manager pour votre cloud privé Azure VMware Solution. Vous pouvez ainsi vous assurer que le connecteur HCX dispose des informations d’identification VCenter Server les plus récentes.

En plus de ce guide pratique, vous pouvez également visionner la vidéo de [réinitialisation des mots de passe vCenter CloudAdmin et NSX-T Admin](https://youtu.be/cK1qY3knj88).

## <a name="reset-your-azure-vmware-solution-credentials"></a>Réinitialiser les informations d’identification Azure VMware Solution

 Tout d’abord, réinitialisons les informations d’identification de vos composants Azure VMare Solution. Vos informations d’identification de CloudAdmin vCenter Server et d’admin NSX-T n’expirent pas. Toutefois, vous pouvez suivre ces étapes pour générer de nouveaux mots de passe pour ces comptes.

> [!NOTE]
> Si vous utilisez vos informations d’identification de CloudAdmin pour des services connectés tels que HCX, vRealize Orchestrator, vRealize Operations Manager ou vMware Horizon, vos connexions cessent de fonctionner une fois que vous avez mis à jour votre mot de passe.  Ces services doivent être arrêtés avant le lancement de la rotation du mot de passe.  Sinon, vous risquez d’obtenir des verrous temporaires sur vos comptes d’administrateur NSX-T et CloudAdmin vCenter, car ces services appelleront en permanence à l’aide de vos anciennes informations d’identification.  Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](./concepts-identity.md).

1. À partir du portail Azure, ouvrez une session Azure Cloud Shell.

2. Exécutez la commande suivante pour mettre à jour votre mot de passe CloudAdmin vCenter.  Vous devez remplacer {SubscriptionID}, {ResourceGroup} et {PrivateCloudName} par les valeurs réelles du cloud privé auquel appartient le compte CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Exécutez la commande suivante pour mettre à jour votre mot de passe d’administrateur NSX-T. Vous devez remplacer {SubscriptionID}, {ResourceGroup} et {PrivateCloudName} par les valeurs réelles du cloud privé auquel appartient le compte d’administrateur NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Vérifier que le connecteur HCX dispose des informations d’identification vCenter Server les plus récentes

Maintenant que vous avez réinitialisé vos informations d’identification, procédez comme suit pour vous assurer que le connecteur HCX dispose de vos informations d’identification mises à jour.

1. Une fois que vous avez modifié votre mot de passe, accédez à l’interface web du connecteur HCX local à l’aide de https://{IP de l’appliance de connecteur HCX}:443. Veillez à utiliser le port 443. Connectez-vous à l’aide de vos nouvelles informations d’identification.

2. Dans le tableau de bord VMware HCX, sélectionnez **Site Pairing (Jumelage de sites)** .
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Capture d’écran du tableau de bord VMware HCX avec l’option Site Pairing mise en surbrillance.":::
 
3. Sélectionnez la connexion correcte à Azure VMware Solution (s’il en existe plusieurs), puis sélectionnez **Modifier la connexion**.
 
4. Fournissez les nouvelles informations d’identification utilisateur de CloudAdmin vCenter Server, puis sélectionnez **Modifier** pour les enregistrer. L’enregistrement doit s’afficher correctement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment réinitialiser les informations d’identification de vCenter Server et de NSX-T Manager pour Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Configurer des composants réseau NSX dans Azure VMware Solution](configure-nsx-network-components-azure-portal.md).
- [Gestion du cycle de vie des machines virtuelles Azure VMware Solution](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Déploiement d’une récupération d’urgence de machines virtuelles à l’aide d’Azure VMware Solution](disaster-recovery-for-virtual-machines.md).