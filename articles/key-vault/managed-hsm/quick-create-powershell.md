---
title: Créer et récupérer des attributs d’une clé managée dans Azure Key Vault – Azure PowerShell
description: Guide de démarrage rapide montrant comment définir et récupérer une clé managée dans Azure Key Vault à l’aide d’Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815429"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Démarrage rapide : Définir et récupérer une clé managée dans Azure Key Vault à l’aide de PowerShell

Dans ce guide de démarrage rapide, vous créez un coffre de clés dans Azure Key Vault avec Azure PowerShell. Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Azure PowerShell vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Une fois que vous avez terminé, vous allez stocker une clé.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, ce tutoriel nécessite le module Azure PowerShell version 1.0.0 ou ultérieure. Tapez `$PSVersionTable.PSVersion` pour connaître la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzAccount` pour créer une connexion avec Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Utilisez l’applet de commande Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) pour créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *westus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Votre ID de principal est retourné au format « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ».

## <a name="create-a-managed-hsm"></a>Créer un HSM managé

Utilisez l’applet de commande Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) pour créer un HSM managé par Key Vault. Vous devrez fournir certaines informations :

- Nom du HSM managé : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).

  > [!Important]
  > Chaque HSM managé doit avoir un nom unique. Remplacez <your-unique-managed-hsm-name> par le nom de votre HSM managé dans les exemples suivants.

- Groupe de ressources nommé : **myResourceGroup**.
- Localisation : **EastUS**.
- Votre ID de principal : passez l’ID de principal Azure Active Directory que vous avez obtenu dans la dernière section au paramètre « Administrateur ». 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

La sortie de cette applet de commande affiche les propriétés du nouveau HSM managé. Notez les deux propriétés ci-dessous :

- **Nom du HSM managé** : Nom que vous avez fourni au paramètre --name ci-dessus.
- **URI du coffre** : Dans l’exemple, il s’agit de https://&lt;nom_unique_de_votre_hsm_managé&gt;.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="activate-your-managed-hsm"></a>Activer votre HSM managé

Toutes les commandes du plan de données sont désactivées tant que le HSM n’est pas activé. Vous ne pourrez pas créer de clés ou attribuer des rôles. Seuls les administrateurs désignés qui ont fait l’objet d’une attribution pendant l’exécution de la commande create peuvent activer le HSM. Pour activer le HSM, vous devez télécharger le [domaine de sécurité](security-domain.md).

Pour activer votre HSM, vous avez besoin de :
- Trois paires de clés RSA au minimum (10 au maximum)
- Spécifier le nombre minimal de clés nécessaires au déchiffrement du domaine de sécurité (quorum)

Pour activer le HSM, vous devez envoyer au moins trois clés publiques RSA au HSM (10 au maximum). Le HSM chiffre le domaine de sécurité avec ces clés et le renvoie. Une fois le téléchargement du domaine de sécurité terminé, votre HSM est prêt à l’emploi. Vous devez aussi spécifier un quorum, qui est le nombre minimal de clés privées nécessaires au déchiffrement du domaine de sécurité.

L’exemple ci-dessous montre comment générer trois certificats autosignés à l’aide de `openssl` (disponible [ici](https://slproweb.com/products/Win32OpenSSL.html) pour Windows).

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Créez et stockez les paires de clés RSA et le fichier de domaine de sécurité générés dans cette étape de manière sécurisée.

Utilisez l’applet de commande Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) pour télécharger le domaine de sécurité et activer votre HSM managé. L’exemple ci-dessous utilise trois paires de clés RSA (seules les clés publiques sont nécessaires pour cette commande) et définit le quorum sur 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Stockez le fichier de domaine de sécurité et les paires de clés RSA de manière sécurisée. Vous en aurez besoin en cas de reprise d’activité ou pour créer un autre HSM managé partageant le même domaine de sécurité et ainsi les mêmes clés.

Une fois le domaine de sécurité téléchargé, votre HSM sera à l’état actif et prêt à l’emploi.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké un certificat. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter la référence des [applets de commande Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
