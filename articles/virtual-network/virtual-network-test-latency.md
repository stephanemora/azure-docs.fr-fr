---
title: Tester la latence du réseau des machines virtuelles Azure dans un réseau virtuel Azure | Microsoft Docs
description: Découvrez comment tester la latence du réseau entre des machines virtuelles Azure sur un réseau virtuel
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587082"
---
# <a name="testing-network-latency"></a>Teste de la latence du réseau

La mesure de la latence du réseau avec un outil conçu pour la tâche donne les résultats les plus précis. Les outils disponibles publiquement tels que SockPerf (pour Linux) et LATTS (pour Windows) sont des exemples d’outils qui peuvent isoler et mesurer la latence du réseau tout en excluant d’autres types de latence, tels que la latence des applications. Ces outils se concentrent sur le type de trafic qui affecte les performances de l’application, à savoir TCP et UDP. D’autres outils de connectivité courants, tels que le test ping, peuvent parfois être utilisés pour mesurer la latence, mais ces résultats peuvent ne pas être représentatifs du trafic utilisé dans les charges de travail réelles. En effet, la plupart de ces outils utilisent le protocole ICMP, qui peut être traité différemment du trafic d’application et les résultats peuvent ne pas être applicables aux charges de travail qui utilisent TCP et UDP. Pour un test précis de la latence du réseau des protocoles utilisés par la plupart des applications, SockPerf (pour Linux) et Latte (pour Windows) produisent les résultats les plus pertinents. Ce document couvre ces deux outils.

## <a name="overview"></a>Vue d'ensemble

À l’aide de deux machines virtuelles, l’une en tant qu’expéditeur et l’autre en tant que récepteur, un canal de communication bidirectionnel est créé pour envoyer et recevoir des paquets dans les deux directions afin de mesurer le temps d’aller-retour (RTT).

Ces étapes peuvent être utilisées pour mesurer la latence du réseau entre deux machines virtuelles (VM) ou même entre deux ordinateurs physiques. Les mesures de latence peuvent être utiles pour les scénarios suivants :

- établir une référence pour la latence du réseau entre les machines virtuelles déployées
- Comparer les effets des modifications de la latence du réseau après les modifications relatives apportées :
  - au logiciel du système d’exploitation ou de la pile réseau, y compris les modifications de configuration
  - à la méthode de déploiement d’une machine virtuelle, tel que le déploiement dans une zone ou un PPG
  - aux propriétés des machines virtuelles, telles que les changements de taille ou de mise en réseau accélérée
  - au réseau virtuel, tel que le routage ou le filtrage des modifications

### <a name="tools-for-testing"></a>aux outils de test
Pour mesurer la latence, nous disposons de deux options différentes, une pour les systèmes basés sur Windows et une autre pour les systèmes basés sur Linux

Pour Windows : latte.exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Pour Linux : SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

L’utilisation de ces outils permet de s’assurer que seuls les délais de livraison de la charge utile TCP ou UDP sont mesurés et non ICMP (test Ping) ou d’autres types de paquets qui ne sont pas utilisés par les applications et n’affectent pas leurs performances.

### <a name="tips-for-an-optimal-vm-configuration"></a>Conseils pour une configuration optimale des machines virtuelles :

- Utilisez la dernière version de Windows ou Linux
- Activez la mise en réseau accélérée pour de meilleurs résultats
- Déployer des machines virtuelles avec le [Groupe de placement de proximité Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Les machines virtuelles plus volumineuses fonctionnent généralement mieux que les machines virtuelles plus petites

### <a name="tips-for-analysis"></a>Conseils pour l’analyse

- Établissez une ligne de base dès le début, dès que le déploiement, la configuration et les optimisations sont terminés
- Comparez toujours les nouveaux résultats à une ligne de base ou d’un test à un autre avec des modifications contrôlées
- Répétez les tests chaque fois que des modifications sont observées ou planifiées


## <a name="testing-vms-running-windows"></a>Test de machines virtuelles en exécutant Windows

## <a name="get-latteexe-onto-the-vms"></a>Obtenir Latte.exe sur les machines virtuelles

Téléchargez la dernière version : [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Envisagez de placer Latte.exe dans un dossier distinct, tel que c:\tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Autoriser Latte.exe via le pare-feu Windows

Sur le RÉCEPTEUR, créez une règle Autoriser sur le pare-feu Windows pour autoriser l’entrée du trafic Latte.exe. Il est plus facile d’autoriser la totalité du programme Latte.exe par nom que d’autoriser des ports entrants TCP spécifiques.

Autorisez Latte.exe via le pare-feu Windows comme ceci :

netsh advfirewall firewall add rule program=\<PATH\>\\latte.exe name=&quot;Latte&quot; protocol=any dir=in action=allow enable=yes profile=ANY


Par exemple, si vous avez copié latte.exe dans le dossier &quot;c:\\tools&quot;, voici la commande :

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Exécution des tests de latence

Démarrez latte.exe sur le RÉCEPTEUR (exécutez à partir de CMD et non à partir de PowerShell) :

latte -a &lt;Adresse IP du récepteur&gt;:&lt;port&gt; -i &lt;itérations&gt;

Des itérations à hauteur de 65 Ko suffisent à renvoyer des résultats représentatifs.

Tout numéro de port disponible est correct.

Si la machine virtuelle possède une adresse IP de 10.0.0.4, la commande ressemble à ceci :

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Démarrez latte.ex sur l’EXPÉDITEUR (exécutez à partir de CMD et non à partir de PowerShell) :

latte -c -a \<Adresse IP du récepteur\>:\<port\> -i \<itérations\>


La commande qui en résulte est la même que sur le récepteur, exception faite de l'ajout de &quot;-c&quot; pour indiquer qu’il s’agit du &quot;client &quot; ou de l’expéditeur :

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Attendez les résultats. Selon l’éloignement des machines virtuelles, cela peut prendre quelques minutes. Vous pouvez commencer par un nombre inférieur d'itérations pour vérifier que cela fonctionne avant d'exécuter de plus long tests.



## <a name="testing-vms-running-linux"></a>Test de machines virtuelles exécutant Linux

Utilisez SockPerf. Elle est disponible à partir de [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Installer SockPerf sur les machines virtuelles

Sur les machines virtuelles Linux (EXPÉDITEUR et RÉCEPTEUR), exécutez ces commandes pour préparer SockPerf sur vos machines virtuelles. Des commandes sont fournies pour le distributions principales.

#### <a name="for-rhel--centos-follow-these-steps"></a>Ces étapes concernent uniquement RHEL/CentOS :
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Pour Ubuntu, exécutez les opérations qui suivent :
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Pour toutes les distributions, copiez, compilez et installez SockPerf selon les étapes suivantes :
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Exécuter SockPerf sur les machines virtuelles

Une fois l’installation de SockPerf terminée, les machines virtuelles sont prêtes à exécuter les tests de latence. 

Commencez par démarrer SockPerf sur le RÉCEPTEUR.

Tout numéro de port disponible est correct. Dans cet exemple, nous utilisons le port 12345 :
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Maintenant que le serveur est à l’écoute, le client peut commencer à envoyer des paquets au serveur sur le port sur lequel il écoute, 12345 dans le cas présent.

Environ 100 secondes sont suffisantes pour retourner les résultats représentatifs, comme indiqué dans l’exemple suivant :
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Attendez les résultats. En fonction de la distance entre les machines virtuelles, le nombre d’itérations varie. Vous pouvez commencer par des tests plus courts d’environ 5 minutes pour vérifier que cela fonctionne avant d'exécuter des tests plus longs.

Cet exemple SockPerf utilise une taille de message de 350 octets, car il s’agit d’un paquet de taille moyenne classique. La taille du message peut être ajustée à une valeur supérieure ou inférieure pour obtenir des résultats qui représentent plus précisément la charge de travail qui s’exécutera sur les machines virtuelles.


## <a name="next-steps"></a>Étapes suivantes
* Améliorer la latence avec le [Groupe de placement de proximité Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Découvrez comment [Optimiser la mise en réseau pour les machines virtuelles](../virtual-network/virtual-network-optimize-network-bandwidth.md) pour votre scénario.
* Découvrez de quelle façon [la bande passante est allouée aux machines virtuelles](../virtual-network/virtual-machine-network-throughput.md)
* En savoir plus avec le [FAQ sur les réseaux virtuels Azure](../virtual-network/virtual-networks-faq.md)