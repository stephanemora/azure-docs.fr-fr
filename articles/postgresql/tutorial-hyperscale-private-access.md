---
title: Créer un groupe de serveurs avec accès privé (préversion) – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Connecter une machine virtuelle au point de terminaison privé d’un groupe de serveurs
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: tutorial
ms.date: 10/15/2021
ms.openlocfilehash: d024f68b68fd381641c0dc7aaf5aedefd3930119
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075155"
---
# <a name="create-server-group-with-private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Créer un groupe de serveurs avec accès privé (préversion) dans Azure Database pour PostgreSQL – Hyperscale (Citus)

Ce tutoriel crée une machine virtuelle et un groupe de serveurs Hyperscale (Citus), puis établit un [accès privé](concepts-hyperscale-private-access.md) entre eux.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Tout d’abord, nous allons configurer un groupe de ressources et un réseau virtuel. Ils contiendront notre groupe de serveurs et notre machine virtuelle.

```sh
az group create \
    --name link-demo \
    --location eastus

az network vnet create \
    --resource-group link-demo \
    --name link-demo-net \
    --address-prefix 10.0.0.0/16

az network nsg create \
    --resource-group link-demo \
    --name link-demo-nsg

az network vnet subnet create \
    --resource-group link-demo \
    --vnet-name link-demo-net \
    --name link-demo-subnet \
    --address-prefixes 10.0.1.0/24 \
    --network-security-group link-demo-nsg
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

À des fins de démonstration, nous allons utiliser une machine virtuelle qui exécute Debian Linux et le client PostgreSQL `psql`.

```sh
# provision the VM
az vm create \
    --resource-group link-demo \
    --name link-demo-vm \
    --vnet-name link-demo-net \
    --subnet link-demo-subnet \
    --nsg link-demo-nsg \
    --public-ip-address link-demo-net-ip \
    --image debian \
    --admin-username azureuser \
    --generate-ssh-keys

# install psql database client
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts \
        "sudo touch /home/azureuser/.hushlogin" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get update" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-client"
```

## <a name="create-a-server-group-with-a-private-link"></a>Créer un groupe de serveurs avec une liaison privée

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.

3. Pour l’option de déploiement, sélectionnez le bouton **Créer** sous **Groupe de serveurs Hyperscale (Citus)**.

4. Remplissez le formulaire de détails du nouveau serveur avec les informations suivantes :

    - **Groupe de ressources** : `link-demo`
    - **Nom du groupe de serveurs** : `link-demo-sg`
    - **Emplacement** : `East US`
    - **Mot de passe** : (votre choix)

    > [!NOTE]
    >
    > Le nom du groupe de serveurs doit être globalement unique dans Azure, car il crée une entrée DNS. Si `link-demo-sg` n’est pas disponible, choisissez un autre nom et ajustez les étapes ci-après en conséquence.

5. Sélectionnez **Configurer le groupe de serveurs**, choisissez le plan **De base**, puis sélectionnez **Enregistrer**.

6. Sélectionnez **Suivant : Réseau** en bas de la page.

7. Sélectionnez **Accès privé (préversion)**.

    > [!NOTE]
    >
    > L’accès privé est disponible en préversion dans [certaines régions](concepts-hyperscale-limits.md#regions) uniquement.
    >
    > Si l’option d’accès privé ne peut pas être sélectionnée pour votre groupe de serveurs, même si votre groupe de serveurs se trouve dans une région autorisée, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Azure et incluez votre ID d’abonnement Azure pour obtenir l’accès.

8. Un écran appelé **Créer un point de terminaison privé** apparaît. Entrez ces valeurs et sélectionnez **OK** :

    - **Groupe de ressources** : `link-demo`
    - **Emplacement** : `(US) East US`
    - **Nom** : `link-demo-sg-c-pe1`
    - **Sous-ressource cible** : `coordinator`
    - **Réseau virtuel** : `link-demo-net`
    - **Sous-réseau** : `link-demo-subnet`
    - **Intégrer à une zone DNS privée** : Oui

9. Après avoir créé le point de terminaison privé, sélectionnez **Vérifier + créer** pour créer votre groupe de serveurs Hyperscale (Citus).

## <a name="access-the-server-group-privately-from-the-virtual-machine"></a>Accéder en privé au groupe de serveurs à partir de la machine virtuelle

La liaison privée permet à notre machine virtuelle de se connecter à notre groupe de serveurs et empêche des hôtes externes de s’y connecter. Au cours de cette étape, nous allons vérifier que le client de base de données `psql` sur notre machine virtuelle peut communiquer avec le nœud coordinateur du groupe de serveurs.

```sh
# save db URI
#
# obtained from Settings -> Connection Strings in the Azure portal
#
# replace {your_password} in the string with your actual password
PG_URI='host=c.link-demo-sg.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require'

# attempt to connect to server group with psql in the virtual machine
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts "psql '$PG_URI' -c 'SHOW citus.version;'" \
    --query 'value[0].message' \
    | xargs printf
```

Vous devez voir un numéro de version pour Citus dans la sortie. Le cas échéant, psql a pu exécuter la commande et la liaison privée a fonctionné.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nous avons vu comment créer une liaison privée entre une machine virtuelle et un groupe de serveurs Hyperscale (Citus). Nous pouvons maintenant déprovisionner les ressources.

Supprimez le groupe de ressources et les ressources qu’il contient seront déprovisionnées :

```sh
az group delete --resource-group link-demo

# press y to confirm
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[accès privé](concepts-hyperscale-private-access.md) (préversion)
* En savoir plus sur les [points de terminaison privés](/azure/private-link/private-endpoint-overview)
* En savoir plus sur les [réseaux virtuels](/azure/virtual-network/concepts-and-best-practices)
* En savoir plus sur les [zones DNS privées](/azure/dns/private-dns-overview)
