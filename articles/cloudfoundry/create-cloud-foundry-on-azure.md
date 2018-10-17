---
title: Création de Cloud Foundry sur Azure
description: Apprenez comment configurer les paramètres nécessaires au provisionnement d’un cluster PCF Cloud Foundry sur Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250579"
---
# <a name="create-cloud-foundry-on-azure"></a>Création de Cloud Foundry sur Azure

Ce didacticiel explique comment créer et générer rapidement les paramètres nécessaires au provisionnement d’un cluster PCF Pivotal Cloud Foundry sur Azure.  La solution Pivotal Cloud Foundry se trouve en effectuant une recherche sur [Azure MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Texte de remplacement de l’image](media/deploy/pcf-marketplace.png "Recherche de Pivotal Cloud Foundry sur Azure")


## <a name="generate-an-ssh-public-key"></a>Générer une clé publique SSH

Il existe plusieurs façons de générer une clé SSH publique sous Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Cliquez ici pour consulter les [instructions]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) correspondant à votre environnement.

## <a name="create-a-service-principal"></a>Créer un principal du service

> [!NOTE]
>
> La création d’un principal de service nécessite l’autorisation d’un compte propriétaire.  De plus, vous pouvez créer un script pour automatiser la création du principal de service. Par exemple, à l’aide de l’interface de ligne de commande Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Connectez-vous à votre compte Azure.

    `az login`

    ![Texte de remplacement de l’image](media/deploy/az-login-output.png "Connexion à l’interface de ligne de commande Azure")
 
    Copiez la valeur « id » comme **ID d’abonnement** et la valeur **tenantId** à utiliser ultérieurement.

2. Définissez votre abonnement par défaut pour cette configuration.

    `az account set -s {id}`

3. Créez une application AAD pour votre PCF et définissez un mot de passe alphanumérique unique.  Enregistrez le mot de passe comme étant votre **clientSecret** devant être utilisé ultérieurement.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copiez la valeur « appId » dans la sortie en tant que **ClientID** devant être utilisé ultérieurement.

    > [!NOTE]
    >
    > Choisissez votre propre page d’accueil d’application et votre identificateur URI.  Exemple : http://www.contoso.com.

4. Créez un principal de service à l’aide de votre nouvel « appId ».

    `az ad sp create --id {appId}`

5. Définissez le rôle d’autorisation de votre principal de service en tant que **Contributeur**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Vous pouvez aussi utiliser…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Texte de remplacement d’image](media/deploy/svc-princ.png "Attribution du rôle du principal de service")

6. Vérifiez que vous pouvez vous connecter à votre principal de service à l’aide de l’appId, du mot de passe et du tenantId.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Créez un fichier.json au format suivant en utilisant toutes les valeurs **subscription ID**, **tenantId**, **clientID** et **clientSecret** que vous avez copié précédemment.  Enregistrez le fichier .

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Récupération du jeton Pivotal Network

1. Enregistrez-vous ou connectez-vous à votre compte [Pivotal Network](https://network.pivotal.io).
2. Cliquez sur votre nom de profil en haut à droite de la page, puis sélectionnez **Edit Profile » (Modifier le profil).
3. Faites défiler jusqu’au bas de la page et copiez la valeur **LEGACY API TOKEN**.  C’est la valeur de votre **jeton Pivotal Network** qui sera utilisée ultérieurement.

## <a name="provision-your-cloud-foundry-on-azure"></a>Provisionnement de votre Cloud Foundry sur Azure

1. Maintenant vous disposez de tous les paramètres nécessaires pour provisionner votre cluster [Pivotal Cloud Foundry sur Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Entrez les paramètres et créez votre cluster PCF.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Vérification du déploiement et connexion au Pivotal Ops Manager

1. Votre cluster PCF doit afficher un état de déploiement.

    ![Texte de remplacement d’image](media/deploy/deployment.png "État du déploiement Azure")

2. Cliquez sur le lien **Déploiements** dans la barre de navigation de gauche pour obtenir les informations d’identification de votre PCF Ops Manager, puis cliquez sur le **nom du déploiement** dans la page suivante.
3. Dans la barre de navigation de gauche, cliquez sur le lien **Sorties** pour afficher l’URL, le nom d’utilisateur et le mot de passe du PCF Ops Manager.  La valeur « OPSMAN-FQDN » correspond à l’URL.
 
    ![Texte de remplacement d’image](media/deploy/deploy-outputs.png "Sortie du déploiement de Cloud Foundry")
 
4. Lancez l’URL dans un navigateur Web et entrez les informations d’identification de l’étape précédente pour vous connecter.

    ![Texte de remplacement d’image](media/deploy/pivotal-login.png "Page de connexion Pivotal")
         
    > [!NOTE]
    >
    > Si le navigateur Internet Explorer échoue à cause d’un message d’avertissement de site non sécurisé, cliquez sur « Plus d’informations » et « Aller à la page Web ».  Si vous utilisez Firefox, cliquez sur Advance et ajoutez la certification pour continuer.

5. Votre PCF Ops Manager doit afficher les instances Azure déployées. Vous pouvez maintenant commencer à déployer et à gérer vos applications ici !
               
    ![Texte de remplacement d’image](media/deploy/ops-mgr.png "Instance Azure déployée dans Pivotal")
 
