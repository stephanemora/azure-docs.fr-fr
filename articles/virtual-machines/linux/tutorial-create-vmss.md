---
title: 'Tutoriel : Créer un groupe de machines virtuelles identiques Linux'
description: Apprenez à créer et déployer une application hautement disponible sur des machines virtuelles Linux à l’aide d’un groupe de machines virtuelles identiques
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machines
ms.collection: linux
ms.date: 10/15/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 10f7202e2525920edd4c65b2e35cea51b9751abb
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165006"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Tutoriel : Créer un groupe de machines virtuelles identiques et déployer une application hautement disponible sur Linux 

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques uniformes

Les groupes de machines virtuelles identiques avec [orchestration Flexible](../flexible-virtual-machine-scale-sets.md) vous permettent de créer et de gérer un groupe de machines virtuelles à charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini.

Ce tutoriel explique comment déployer un groupe de machines virtuelles identiques dans Azure et vous apprend à :

> [!div class="checklist"]
> * Créez un groupe de ressources.
> * Créer un groupe identique Flexible avec un équilibreur de charge.
> * Ajouter nginx aux instances du groupe identique.
> * Ouvrir le port 80 au trafic HTTP.
> * Tester le groupe identique.


## <a name="scale-set-overview"></a>Vue d’ensemble des groupes identiques

Les groupes identiques vous offrent les avantages suivants :
- Création et gestion faciles de plusieurs machines virtuelles
- Offre une haute disponibilité et une résilience d’application en répartissant les machines virtuelles entre les domaines d’erreur
- Permet une mise à l’échelle automatique de votre application en fonction des variations du besoin en ressources
- Fonctionne à grande échelle

Avec l’orchestration Flexible, Azure offre une expérience unifiée sur tout l’écosystème de machines virtuelles Azure. L’orchestration Flexible garantit une haute disponibilité (jusqu’à 1 000 machines virtuelles) en répartissant les machines virtuelles entre différents domaines d’erreur dans une région ou dans une zone de disponibilité. Vous pouvez ainsi effectuer un scale-out de votre application tout en conservant l’isolation du domaine d’erreur, essentielle pour exécuter des charges de travail avec état ou basées sur un quorum, y compris :
- Charges de travail basées sur un quorum
- Bases de données open source
- Applications avec état
- Services nécessitant une haute disponibilité et une grande échelle
- Services cherchant à combiner différents types de machines virtuelles ou à tirer parti à la fois de machines virtuelles spot et à la demande
- Applications d’un groupe à haute disponibilité existant

Apprenez-en davantage sur les différences entre les groupes identiques uniformes et les groupes identiques flexibles dans [Modes d’orchestration](../../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md).



## <a name="create-a-scale-set"></a>Créer un groupe identique

Utilisez le portail Azure pour créer un groupe identique Flexible.

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez **Groupes de machines virtuelles identiques**.
1. Sélectionnez **Créer** dans la page **Groupes de machines virtuelles identiques**. La page **Créer un groupe de machines virtuelles identiques** s’ouvre.
1. Sélectionnez l’abonnement à utiliser dans **Abonnement**.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et tapez *myVMSSRG* pour le nom, puis sélectionnez **OK**.
    :::image type="content" source="media/tutorial-create-vmss/flex-project-details.png" alt-text="Détails du projet.":::
1. Dans **Nom du groupe de machines virtuelles identiques**, tapez *myVMSS*.
1. Dans **Région**, sélectionnez une région proche de vous comme *USA Est*.
    :::image type="content" source="media/tutorial-create-vmss/flex-details.png" alt-text="Nom et région.":::
1. Laissez la **zone de disponibilité** non renseignée pour cet exemple.
1. Pour **Mode d’orchestration**, sélectionnez **Flexible**.
1. Laissez la valeur par défaut *1* pour le nombre de domaines d’erreur ou choisissez une autre valeur dans la liste déroulante.
   :::image type="content" source="media/tutorial-create-vmss/flex-orchestration.png" alt-text="Choisissez le mode d’orchestration Flexible.":::
1. Pour **Image**, sélectionnez *Ubuntu 18.04 LTS*.
1. Pour **Taille**, laissez la valeur par défaut ou sélectionnez une taille comme *Standard_E2s_V3*.
1. Dans **Nom d’utilisateur**, tapez *azureuser*.
1. Pour **Source de clé publique SSH**, laissez la valeur par défaut **Générer une nouvelle paire de clés**, puis tapez *myKey* comme **Nom de la paire de clés**.
    :::image type="content" source="media/tutorial-create-vmss/flex-admin.png" alt-text="Capture d’écran de la section Compte d’administrateur où vous sélectionnez un type d’authentification et où fournissez les informations d’identification de l’administrateur.":::
1. Sous l’onglet **Réseaux**, sous **Équilibrage de charge**, sélectionnez **Utiliser un équilibreur de charge**.
1. Pour les **options d’équilibrage de charge**, laissez la valeur par défaut **Équilibrage de charge Azure**.
1. Pour **Sélectionner un équilibreur de charge**, choisissez **Créer**.
    :::image type="content" source="media/tutorial-create-vmss/load-balancer-settings.png" alt-text="Paramètres de l’équilibreur de charge.":::
1. Dans la page **Créer un équilibreur de charge**, tapez un nom pour votre équilibreur de charge et le **nom de l’adresse IP publique**.
1. Pour **Étiquette de nom de domaine**, tapez un nom à utiliser comme préfixe pour votre nom de domaine. Ce nom doit être unique.
1. Lorsque vous avez terminé, sélectionnez **Créer**.
    :::image type="content" source="media/tutorial-create-vmss/flex-load-balancer.png" alt-text="Créez un équilibreur de charge.":::
1. De retour sous l’onglet **Réseaux**, laissez le nom par défaut pour le pool back-end.
1. Sous l’onglet **Mise à l’échelle**, laissez le nombre d’instances par défaut défini sur *2* ou ajoutez votre propre valeur. Il s’agit du nombre de machines virtuelles qui seront créées. Tenez compte des coûts et des limites de votre abonnement si vous modifiez cette valeur.
1. Laissez la **stratégie de mise à l’échelle** définie sur *Manuelle*.
    :::image type="content" source="media/tutorial-create-vmss/flex-scaling.png" alt-text="Paramètres de la stratégie de mise à l’échelle.":::
1. Sélectionnez l'onglet **Avancé** .
1. Sous **Données personnalisées et cloud init**, copiez ce qui suit et collez-le dans la zone de texte **Données personnalisées** :
    ```yml
    #cloud-config
    package_upgrade: true
    packages:
      - nginx
      - nodejs
      - npm
    write_files:
      - owner: www-data:www-data
      - path: /etc/nginx/sites-available/default
        content: |
          server {
            listen 80;
            location / {
              proxy_pass http://localhost:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection keep-alive;
              proxy_set_header Host $host;
              proxy_cache_bypass $http_upgrade;
            }
          }
      - owner: azureuser:azureuser
      - path: /home/azureuser/myapp/index.js
        content: |
          var express = require('express')
          var app = express()
          var os = require('os');
          app.get('/', function (req, res) {
            res.send('Hello World from host ' + os.hostname() + '!')
          })
          app.listen(3000, function () {
            console.log('Hello world app listening on port 3000!')
          })
    runcmd:
      - service nginx restart
      - cd "/home/azureuser/myapp"
      - npm init
      - npm install express -y
      - nodejs index.js
    ```
1. Quand vous avez terminé, sélectionnez **Vérifier + créer**.
1. Une fois que vous voyez que la validation a abouti, vous pouvez sélectionner **Créer** au bas de la page pour déployer votre groupe identique.
1. Quand la fenêtre **Générer une nouvelle paire de clés** s’ouvre, sélectionnez **Télécharger la clé privée et créer une ressource**. Votre fichier de clé sera téléchargé en tant que **myKey.pem**. Notez où le fichier `.pem` a été téléchargé ; vous aurez besoin du chemin à l’étape suivante.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** pour voir votre groupe identique.


## <a name="view-the-vms-in-your-scale-set"></a>Voir les machines virtuelles incluses dans votre groupe identique

Dans la page du groupe identique, sélectionnez **Instances** dans le menu gauche. 

La liste des machines virtuelles qui font partie de votre groupe identique s’affiche. La liste comprend :

- Nom de la machine virtuelle
- Nom de l’ordinateur utilisé par la machine virtuelle.
- État actuel de la machine virtuelle, par exemple *En cours d’exécution*.
- *État de provisionnement* de la machine virtuelle, par exemple, *Opération réussie*.

:::image type="content" source="media/tutorial-create-vmss/instances.png" alt-text="Tableau des informations sur les instances du groupe identique.":::


## <a name="open-port-80"></a>Ouverture du port 80 

Ouvrez le port 80 sur votre groupe identique en ajoutant une règle de trafic entrant à votre groupe de sécurité réseau (NSG).

1. Dans la page de votre groupe identique, sélectionnez **Réseaux** dans le menu de gauche. La page **Réseaux** s’ouvre.
1. Sélectionnez **Ajouter une règle de port d’entrée**. La page **Ajouter une règle de sécurité de trafic entrant** s’ouvre.
1. Sous **Service**, sélectionnez *HTTP*, puis sélectionnez **Ajouter** au bas de la page.

## <a name="test-your-scale-set"></a>Tester votre groupe identique

Testez votre groupe identique en vous y connectant à partir d’un navigateur.

1. Dans la page **Vue d’ensemble** de votre groupe identique, copiez l’adresse IP publique.
1. Ouvrez un autre onglet dans votre navigateur et collez l’adresse IP dans la barre d’adresse.
1. Quand la page se charge, notez le nom de l’ordinateur qui est affiché. 
1. Actualisez la page jusqu’à ce que le nom de l’ordinateur change. 

## <a name="delete-your-scale-set"></a>Supprimer votre groupe identique

Quand vous avez terminé, vous devez supprimer le groupe de ressources, ce qui va supprimer tout ce que vous avez déployé pour votre groupe identique.

1. Dans la page de votre groupe identique, sélectionnez le **groupe de ressources**. La page de votre groupe de ressources s’ouvre.
1. En haut de la page, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la page **Voulez-vous vraiment supprimer**, tapez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment créer un groupe de machines virtuelles identiques. Vous avez appris à :

> [!div class="checklist"]
> * Créez un groupe de ressources.
> * Créer un groupe identique Flexible avec un équilibreur de charge.
> * Ajouter nginx aux instances du groupe identique.
> * Ouvrir le port 80 au trafic HTTP.
> * Tester le groupe identique.

Passez au didacticiel suivant pour en savoir plus sur les concepts de l’équilibrage de charge des machines virtuelles.

> [!div class="nextstepaction"]
> [Équilibrage de charge des machines virtuelles](tutorial-load-balancer.md)
