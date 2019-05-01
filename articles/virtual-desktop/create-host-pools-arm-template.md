---
title: Créer un pool d’hôte de Windows Virtual Desktop Preview avec un modèle Azure Resource Manager - Azure
description: Comment créer un pool d’hôte dans la version préliminaire de Windows Virtual Desktop avec un modèle Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: b1c7d1fe561ebc11f88644a5bf2fd8bda1c9a6c6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924991"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Créer un pool d’hôtes avec le modèle Azure Resource Manager

Les pools d’hôtes sont une collection d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop Preview. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

Suivez les instructions de cette section pour créer un pool d’hôte pour un client de bureau virtuel Windows avec un modèle Azure Resource Manager fourni par Microsoft. Cet article vous indique comment créer un pool de l’hôte dans un bureau virtuel Windows, créez un groupe de ressources avec des machines virtuelles dans un abonnement Azure, joindre ces machines virtuelles au domaine AD et inscrire les machines virtuelles avec le bureau virtuel Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Ce que vous devez exécuter le modèle Azure Resource Manager

Vérifiez que vous connaissez les opérations suivantes avant d’exécuter le modèle Azure Resource Manager :

- Lorsque la source de l’image que vous souhaitez utiliser est. Il est à partir de la galerie Azure ou est personnalisée ?
- Vos informations d’identification de jonction de domaine.
- Vos informations d’identification de bureau virtuel Windows.

Lorsque vous créez un pool d’hôte de bureau virtuel Windows avec le modèle Azure Resource Manager, vous pouvez créer une machine virtuelle à partir de la galerie Azure, d’une image managée ou d’une image non managée. Pour en savoir plus sur la création d’images de machine virtuelle, consultez [préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) et [créer une image managée d’une machine virtuelle généralisée dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Exécutez le modèle Azure Resource Manager pour l’approvisionnement d’un nouveau pool d’hôte

Pour commencer, accédez à [cette URL GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Déployer le modèle sur Azure

Si vous effectuez un déploiement dans un abonnement entreprise, faites défiler vers le bas et sélectionnez **déployer sur Azure**, puis ahead skip renseignez les paramètres en fonction de votre source de l’image.

Si vous effectuez un déploiement dans un abonnement de fournisseur de solutions Cloud, suivez ces étapes pour déployer sur Azure :

1. Faites défiler et cliquez sur **déployer sur Azure**, puis sélectionnez **emplacement du lien de copie**.
2. Ouvrez un éditeur de texte tel que le bloc-notes et collez le lien.
3. Juste après « https://portal.azure.com/» et avant le mot-dièse (#), entrez un arobase (@) suivi du nom de domaine de locataire. Voici un exemple du format que vous devez utiliser : https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Connectez-vous au portail Azure en tant qu’utilisateur avec des autorisations d’administrateur/contributeur à l’abonnement fournisseur de solutions Cloud.
5. Collez le lien que vous avez copié à l’éditeur de texte dans la barre d’adresses.

Pour obtenir des conseils sur les paramètres, vous devez entrer pour votre scénario, voir le bureau virtuel Windows [fichier Lisez-moi](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Le fichier Lisez-moi est toujours mis à jour avec les dernières modifications.

## <a name="assign-users-to-the-desktop-application-group"></a>Affecter des utilisateurs au groupe d’applications de bureau

Une fois le modèle GitHub Azure Resource Manager est terminée, attribuer l’accès utilisateur avant de commencer à tester les bureaux de session complète sur vos machines virtuelles.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Pour affecter des utilisateurs au groupe d’applications de bureau, ouvrez une fenêtre PowerShell et exécutez la commande suivante pour vous connecter à l’environnement de bureau virtuel Windows :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ensuite, définissez le contexte pour le groupe de client spécifié dans le modèle Azure Resource Manager avec cette applet de commande :

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Après cela, ajoutez les utilisateurs au groupe d’applications de bureau avec cette applet de commande :

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

L’UPN (nom d’utilisateur principal) de l’utilisateur doit correspondre à l’identité de l’utilisateur dans Azure Active Directory (par exemple, user1@contoso.com). Si vous voulez ajouter plusieurs utilisateurs, vous devez exécuter cette applet de commande pour chaque utilisateur.

Une fois ces étapes terminées, les utilisateurs ajoutés au groupe d’applications de bureau peuvent se connecter à Windows Virtual Desktop avec les clients Bureau à distance pris en charge et voir une ressource pour le bureau d’une session.

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Windows Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Windows Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool hôte. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).