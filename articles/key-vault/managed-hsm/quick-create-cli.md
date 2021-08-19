---
title: Démarrage rapide – Provisionner et activer un HSM managé Azure
description: Démarrage rapide montrant comment provisionner et activer un HSM managé à l’aide d’Azure CLI
services: key-vault
author: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 06/21/2021
ms.author: mbaldwin
ms.openlocfilehash: be8c28f736e58ebcbe95d4b8a4042b79fa1cf9f7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443588"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Démarrage rapide : Provisionner et activer un HSM managé à l’aide d’Azure CLI

Azure Key Vault Managed HSM est un service cloud complètement managé, à haut niveau de disponibilité et monolocataire qui vous permet de protéger les clés de chiffrement de vos applications cloud à l’aide de HSM certifiés **FIPS 140-2 de niveau 3**. Pour plus d’informations Managed HSM, vous pouvez consultez la [présentation](overview.md). 

Dans ce démarrage rapide, vous allez créer et activer un HSM managé avec Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous devez disposer des éléments suivants :

* Un abonnement à Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI version 2.25.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).
* HSM managé dans votre abonnement. Consultez [Démarrage rapide : Provisionner et activer un HSM managé à l’aide d’Azure CLI](quick-create-cli.md) pour provisionner et activer un HSM managé.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper la commande suivante :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *ContosoResourceGroup* à l’emplacement *eastus2*.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Créer un HSM managé

La création d’un HSM managé est un processus en deux étapes :
1. Provisionnez une ressource Managed HSM.
1. Activez votre HSM managé en téléchargeant le domaine de sécurité.

### <a name="provision-a-managed-hsm"></a>Provisionner un HSM managé

Utilisez la commande `az keyvault create` pour créer un HSM managé. Ce script a trois paramètres obligatoires : le nom de groupe de ressources, le nom du HSM et l’emplacement géographique.

Vous devez fournir les entrées suivantes pour créer une ressource Managed HSM :
- Groupe de ressources dans lequel il sera placé dans votre abonnement.
- Emplacement Azure.
- Liste des administrateurs initiaux.

L’exemple ci-dessous crée un HSM nommé **ContosoMHSM** dans le groupe de ressources **ContosoResourceGroup**, qui réside à l’emplacement **USA Est 2**, avec **l’utilisateur actuellement connecté** comme unique administrateur et **une période de conservation de 28 jours** pour la suppression réversible. En savoir plus sur la [suppression réversible d’un HSM managé](soft-delete-overview.md)

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid --retention-days 28
```

> [!NOTE]
> L’exécution de la commande create peut prendre quelques minutes. Une fois celle-ci terminée, vous êtes prêt à activer votre HSM.

La sortie de cette commande affiche les propriétés du HSM managé que vous avez créé. Les deux propriétés les plus importantes sont :

* **nom** : Dans l’exemple, son nom est ContosoMHSM. Vous allez utiliser ce nom pour d’autres commandes Key Vault.
* **hsmUri** : Dans l’exemple, l’URI est « https://contosohsm.managedhsm.azure.net  ». les applications qui utilisent votre HSM via son API REST doivent utiliser cet URI.

Votre compte Azure est pour l’instant le seul autorisé à effectuer des opérations sur ce HSM managé. À l’heure actuelle, personne d’autre n’y est autorisé.

### <a name="activate-your-managed-hsm"></a>Activer votre HSM managé

Toutes les commandes du plan de données sont désactivées tant que le HSM n’est pas activé. Vous ne pourrez pas créer de clés ou attribuer des rôles. Seuls les administrateurs désignés qui ont fait l’objet d’une attribution pendant l’exécution de la commande create peuvent activer le HSM. Pour activer le HSM, vous devez télécharger le [domaine de sécurité](security-domain.md).

Pour activer votre HSM, vous avez besoin de :
- Trois paires de clés RSA au minimum (10 au maximum)
- Spécifier le nombre minimal de clés nécessaires au déchiffrement du domaine de sécurité (quorum)

Pour activer le HSM, vous devez envoyer au moins trois clés publiques RSA au HSM (10 au maximum). Le HSM chiffre le domaine de sécurité avec ces clés et le renvoie. Une fois le téléchargement du domaine de sécurité terminé, votre HSM est prêt à l’emploi. Vous devez aussi spécifier un quorum, qui est le nombre minimal de clés privées nécessaires au déchiffrement du domaine de sécurité.

L’exemple ci-dessous montre comment générer trois certificats autosignés à l’aide de `openssl`.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Créez et stockez les paires de clés RSA et le fichier de domaine de sécurité générés dans cette étape de manière sécurisée.

Utilisez la commande `az keyvault security-domain download` pour télécharger le domaine de sécurité et activer votre HSM managé. L’exemple ci-dessous utilise trois paires de clés RSA (seules les clés publiques sont nécessaires pour cette commande) et définit le quorum sur 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Stockez le fichier de domaine de sécurité et les paires de clés RSA de manière sécurisée. Vous en aurez besoin en cas de reprise d’activité ou pour créer un autre HSM managé partageant le même domaine de sécurité et ainsi les mêmes clés.

Une fois le domaine de sécurité téléchargé, votre HSM sera à l’état actif et prêt à l’emploi.

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres guides de démarrage rapide et didacticiels de cette collection reposent sur ce guide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, ainsi que toutes les ressources associées. Vous pouvez supprimer les ressources comme suit :

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés Key Vault et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire une [présentation de Managed HSM](overview.md)
- Découvrir la [gestion des clés dans un HSM managé](key-management.md)
- En savoir plus sur la [Gestion des rôles pour un HSM géré](role-management.md)
- Passer en revue les [bonnes pratiques de Managed HSM](best-practices.md)
