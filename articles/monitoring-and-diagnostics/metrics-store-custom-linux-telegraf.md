---
title: Collecter des mesures personnalisées pour une VM Linux avec l’Agent InfluxData Telegraf
description: Collecter des mesures personnalisées pour une VM Linux avec l’Agent InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990703"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Collecter des mesures personnalisées pour une VM Linux avec l’Agent InfluxData Telegraf

Azure Monitor vous permet de collecter des métriques personnalisées par le biais des données de télémétrie de votre application, d’un agent s’exécutant sur vos ressources Azure ou même d’un système de surveillance d’interaction passive, puis de les soumettre directement à Azure Monitor. Cet article comporte des instructions sur le déploiement d’Agent [InfluxData](https://www.influxdata.com/) Telegraf sur une VM Linux dans Azure et la configuration de l’agent pour publier des métriques dans Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agent InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) est un agent piloté par les plug-in qui active la collection de métriques provenant de plus de 150 sources différentes. Selon les charges de travail exécutées sur votre VM (par ex. MySQL, NGINX, Apache, etc.), vous pouvez configurer l’agent pour tirer parti des plug-ins d’entrée spécialisés afin de collecter les métriques. Sortez les plug-ins, puis activez l’agent pour écrire vers les destinations de votre choix. L’agent Telegraf s’est intégré directement avec l’API REST des métriques personnalisées Azure Monitor et prend en charge un «  plug-in de sortie d’Azure Monitor ». Il permet à l’agent de collecter des mesures spécifiques concernant la charge de travail sur votre VM Linux et de les envoyer en tant que métriques personnalisées à Azure Monitor. 

 ![Présentation de l’Agent Telegraf](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Envoyer des métriques personnalisées 

Pour les besoins de ce didacticiel, nous déployons une VM Linux exécutant le système d’exploitation Ubuntu 16.04 LTS. L’agent Telegraf est pris en charge sur la plupart des systèmes d’exploitation Linux. Les packages Debian et RPM, ainsi que les fichiers binaires décompressés de Linux, sont disponibles sur le portail de téléchargement InfluxData. Consultez ce guide d’installation pour des instructions et options supplémentaires concernant l’installation de Telegraf. 

Se connecter au [portail Azure](https://portal.azure.com)

Pour créer une nouvelle machine virtuelle Linux : 

1. Cliquez sur l’option  **Créer une ressource**  dans le volet de navigation gauche. 
1. Recherchez *Machine virtuelle*  
1. Sélectionnez *Ubuntu 16.04 LTS* et cliquez sur **Créer** 
1. Donnez un nom à votre machine virtuelle comme  *MyTelegrafVM*.  
1. Gardez le type de disque **SSD**, puis indiquez un **nom d’utilisateur**, par exemple,  *azureuser*. 
1. Pour le  *Type d’authentification*, sélectionnez un  **Mot de passe**, puis tapez un mot de passe que vous utiliserez ultérieurement pour vous connecter avec SSH sur cette machine virtuelle. 
1. Choisissez de  **créer un nouveau groupe de ressources** pour créer un groupe de ressources, puis indiquez un nom, par exemple  *myResourceGroup*.  Choisissez l’Emplacement souhaité, puis sélectionnez  **OK**. 

     ![Créer une machine virtuelle Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Choisissez la taille de la machine virtuelle. Vous pouvez filtrer par Type de calcul ou par Type de disque, par exemple. 

     ![Présentation de l’Agent Telegraf Taille de Machine virtuelle](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Dans la  **page Paramètres**, dans  **Réseau** > **Groupe de sécurité réseau** > **Sélectionner des ports d’entrée publics**, sélectionnez  *HTTP*  et  *SSH (22)*. Laissez les autres valeurs par défaut et sélectionnez  **OK**. 

1. Sur la page de résumé, sélectionnez Créer pour démarrer le déploiement de la machine virtuelle. 

1. La machine virtuelle est épinglée au tableau de bord du Portail Azure. Une fois le déploiement terminé, le récapitulatif de la machine virtuelle s’ouvre automatiquement. 

1. Dans le panneau de la machine virtuelle, accédez à l’onglet **Identité** et vérifiez que l’affectation de l’identité système est *activée* sur votre machine virtuelle. 
 
![remplissage](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle 

Créez une connexion SSH avec la machine virtuelle. Sélectionnez le bouton Se connecter sur la page de présentation de votre machine virtuelle. 

![remplissage](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

Sur la page Se connecter à la machine virtuelle, conservez les options par défaut pour vous connecter par nom de DNS sur le port 22. Dans Se connecter à l’aide d’un compte local de machine virtuelle, une commande de connexion s’affiche. Cliquez sur le bouton pour copier la commande. L’exemple suivant montre la commande de connexion SSH : 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Collez la commande de connexion SSH dans un interpréteur de commandes, par exemple, Azure Cloud Shell, Bash sur Ubuntu ou sous Windows, ou utilisez un client SSH de votre choix pour créer la connexion. 

## <a name="install-and-configure-telegraf"></a>Installer et configurer Telegraf 

Pour installer le package Debian Telegraf sur la machine virtuelle, exécutez les commandes suivantes à partir de votre session SSH : 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Le fichier de configuration de Telegraf définit les opérations de Telegraf. Par défaut, un fichier de configuration exemple est installé dans le chemin d’accès « /etc/telegraf/telegraf.conf ». Le fichier de configuration exemple répertorie tous les plug-ins d’entrée et de sortie possibles. Toutefois, nous allons créer un fichier de configuration personnalisé, que l’agent utilisera en exécutant les commandes suivantes 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> La méthode ci-dessus active uniquement deux plug-ins d’entrée « cpu » et « mem », n’hésitez pas à en ajouter plus (Docker, MySQL, NGINX, etc.) en fonction de la charge de travail exécutée sur votre ordinateur. Vous trouverez dans cet article une liste complète des plug-ins d’entrée. 

Enfin, pour que l’agent démarre avec la nouvelle configuration, nous forçons l’arrêt et le redémarrage de l’agent en exécutant les commandes suivantes 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Désormais, l’agent collecte des métriques de chacun des plug-ins d’entrée spécifiés et les envoie à Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Tracer vos métriques Telegraf dans le portail Azure 

1. Ouvrez le [portail Azure](https://portal.azure.com) 

1. Accédez au nouvel onglet Surveiller, puis sélectionnez  **Métriques**.  
     ![remplissage](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Sélectionnez votre machine virtuelle dans le sélecteur de ressources

     ![remplissage](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Sélectionnez l’espace de noms *Telegraf/CPU*, puis sélectionnez la métrique *usage_system*. Vous pouvez choisir de filtrer selon les dimensions de cette métrique, ou de les fractionner.  

     ![remplissage](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuration supplémentaire 

La procédure pas à pas ci-dessus explique comment configurer l’agent Telegraf pour collecter les métriques à partir de plusieurs plug-ins d’entrée de base. L’agent Telegraf prend en charge plus de 150 plug-ins d’entrée, avec certains prenant en charge des options de configuration supplémentaires. InfluxData a publié une [liste des plug-ins pris en charge](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) et des instructions sur [comment les configurer](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

En outre, cette procédure pas à pas vous a permis d’utiliser un agent Telegraf pour envoyer des métriques concernant la machine virtuelle sur laquelle l’agent est déployé. L’agent Telegraf peut également servir de collecteur et de redirecteur de métriques pour d’autres ressources. Pour savoir comment configurer l’agent afin d’émettre des métriques pour les autres ressources Azure, consultez [Sortie de métriques personnalisée Azure Monitor pour Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Supprimer des ressources 

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour cela, sélectionnez le groupe de ressources de la machine virtuelle, sélectionnez Supprimer, puis confirmez le nom du groupe de ressources à supprimer. 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).


