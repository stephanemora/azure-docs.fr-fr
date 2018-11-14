---
title: Résoudre les problèmes d’Azure IoT Edge | Microsoft Docs
description: Résoudre les problèmes courants et découvrir les compétences de dépannage requises pour Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6728ea6e8c8323ed3d418a018de0ad64b7af8033
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283255"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problèmes courants et résolutions pour Azure IoT Edge

Si vous rencontrez des problèmes lors de l’exécution d’Azure IoT Edge dans votre environnement, consultez cet article qui vous guidera pour la résolution des problèmes. 

## <a name="standard-diagnostic-steps"></a>Étapes de diagnostic standard 

Quand vous rencontrez un problème, obtenez des informations supplémentaires sur l’état de votre appareil IoT Edge en examinant les journaux du conteneur et les messages transitant par l’appareil. Utilisez les commandes et les outils de cette section pour recueillir des informations. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Vérifiez l’état du gestionnaire de sécurité IoT Edge et de ses journaux :

Sur Linux :
- Pour voir l’état du gestionnaire de sécurité IoT Edge :

   ```bash
   sudo systemctl status iotedge
   ```

- Pour voir les journaux du gestionnaire de sécurité IoT Edge :

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Pour voir des journaux plus détaillés du gestionnaire de sécurité IoT Edge :

   - Modifiez les paramètres du démon iotedge :

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Mettez à jour les lignes suivantes :
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Redémarrez le démon de sécurité IoT Edge :
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Sous Windows :
- Pour voir l’état du gestionnaire de sécurité IoT Edge :

   ```powershell
   Get-Service iotedge
   ```

- Pour voir les journaux du gestionnaire de sécurité IoT Edge :

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Si le gestionnaire de sécurité IoT Edge n’est pas en cours d’exécution, vérifiez votre fichier de configuration yaml

> [!WARNING]
> Les fichiers YAML ne peuvent pas contenir de tabulations en guise de mise en retrait. Utilisez 2 espaces à la place.

Sur Linux :

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Sous Windows :

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Vérifier si les journaux des conteneurs indiquent des problèmes

Une fois le démon de sécurité IoT Edge exécuté, vérifiez si les journaux des conteneurs indiquent des problèmes. Commencez par les conteneurs déployés, puis examinez les conteneurs qui composent le runtime IoT Edge : Edge Agent et Edge Hub. En général, les journaux Edge Agent fournissent des informations sur le cycle de vie de chaque conteneur. Les journaux Edge Hub fournissent des informations sur la messagerie et le routage. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Afficher les messages acheminés via le hub Edge

Vous pouvez afficher les messages acheminés via le hub Edge et recueillir des insights à partir des journaux détaillés issus des conteneurs du runtime. Pour activer les journaux détaillés sur ces conteneurs, définissez `RuntimeLogLevel` dans votre fichier de configuration yaml. Pour ouvrir le fichier :

Sur Linux :

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Sous Windows :

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Par défaut, l’élément `agent` se présente comme dans l’exemple suivant :

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Remplacez `env: {}` par :

> [!WARNING]
> Les fichiers YAML ne peuvent pas contenir de tabulations en guise de mise en retrait. Utilisez 2 espaces à la place.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Enregistrez le fichier et redémarrez le gestionnaire de sécurité IoT Edge.

Vous pouvez également vérifier les messages envoyés entre IoT Hub et les appareils IoT Edge. Affichez ces messages à l’aide de l’extension [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pour Visual Studio Code. Pour plus d’informations, consultez [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Redémarrer les conteneurs
Après avoir examiné les journaux et les messages pour obtenir plus d’informations, vous pouvez essayer de redémarrer les conteneurs :

```
iotedge restart <container name>
```

Redémarrez les conteneurs du runtime IoT Edge :

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Redémarrer le gestionnaire de sécurité IoT Edge

Si le problème persiste, vous pouvez essayer de redémarrer le gestionnaire de sécurité IoT Edge.

Sur Linux :

   ```cmd
   sudo systemctl restart iotedge
   ```

Sous Windows :

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge Agent s’arrête après environ une minute

Edge Agent démarre et s’exécute avec succès pendant environ une minute, puis s’arrête. Les journaux indiquent qu’Edge Agent tente de se connecter à IoT Hub via AMQP, puis essaie de se connecter à l’aide d’AMQP sur WebSocket. Lorsque cette tentative échoue, Edge Agent se ferme. 

Exemples de journaux Edge Agent :

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Cause racine
Une configuration de mise en réseau sur le réseau hôte empêche Edge Agent d’atteindre le réseau. L’agent tente de se connecter via AMQP (port 5671) en premier. Si la connexion échoue, il essaie via les WebSockets (port 443).

Le runtime IoT Edge configure un réseau pour chacun des modules sur lesquels communiquer. Sur Linux, ce réseau est un réseau de pont. Sur Windows, il utilise NAT. Ce problème est plus courant sur les appareils Windows utilisant des conteneurs Windows qui utilisent le réseau NAT. 

### <a name="resolution"></a>Résolution :
Assurez-vous qu’il existe un itinéraire vers Internet pour les adresses IP affectées à ce réseau pont/NAT. Parfois, une configuration VPN sur l’hôte remplace le réseau IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>Edge Hub ne parvient pas à démarrer

Edge Hub ne parvient pas à démarrer et le message suivant est consigné dans les journaux : 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Cause racine
Un autre processus de l’ordinateur hôte est lié au port 443. Edge Hub mappe les ports 5671 et 443 pour une utilisation dans les scénarios de passerelle. Ce mappage de port échoue si un autre processus est déjà lié à ce port. 

### <a name="resolution"></a>Résolution :
Recherchez et arrêtez le processus qui utilise le port 443. Ce processus est généralement un serveur web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge Agent ne peut pas accéder à l’image d’un module (403)
Un conteneur ne s’exécute pas et les journaux Edge Agent comportent une erreur 403. 

### <a name="root-cause"></a>Cause racine
Edge Agent ne possède pas les autorisations pour accéder à l’image d’un module. 

### <a name="resolution"></a>Résolution :
Vérifiez que les informations d’identification du registre sont correctement spécifiées dans le manifeste de déploiement.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Le démon de sécurité IoT Edge échoue avec un nom d’hôte non valide

Échec de la commande `sudo journalctl -u iotedge` et affichage du message suivant : 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Cause racine
Le runtime IoT Edge peut prendre uniquement en charge les noms d’hôte avec moins de 64 caractères. Les machines physiques n’ont généralement pas de noms d’hôte longs, mais le problème est plus courant sur une machine virtuelle. Les noms d’hôtes générés automatiquement pour les machines virtuelles Windows hébergées dans Azure, en particulier, sont généralement longs. 

### <a name="resolution"></a>Résolution :
Lorsque vous voyez cette erreur, vous pouvez la résoudre par la configuration du nom DNS de votre machine virtuelle, puis en définissant le nom DNS en tant que nom d’hôte dans la commande d’installation.

1. Dans le Portail Azure, accédez au panneau Vue d’ensemble de votre machine virtuelle. 
2. Sélectionnez **configurer** sous le nom DNS. Si votre machine virtuelle a déjà un nom DNS configuré, vous n’avez pas besoin d’en configurer un nouveau. 

   ![Configurer un nom DNS](./media/troubleshoot/configure-dns.png)

3. Fournissez une valeur pour **l’étiquette de nom DNS** et sélectionnez **Enregistrer**.
4. Copiez le nouveau nom DNS, qui doit être au format **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. À l’intérieur de la machine virtuelle, utilisez la commande suivante pour installer le runtime IoT Edge avec votre nom DNS :

   - Sur Linux :

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Sous Windows :

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problèmes de stabilité sur les appareils avec contraintes de ressources 
Vous pouvez rencontrer des problèmes de stabilité sur des appareils avec contraintes, comme le Raspberry Pi, en particulier quand ils sont utilisés comme passerelle. Les symptômes incluent des exceptions de mémoire insuffisante dans le module Edge Hub, des appareils en aval qui ne peuvent pas se connecter ou l’appareil qui cesse d’envoyer des messages de télémétrie au bout de quelques heures.

### <a name="root-cause"></a>Cause racine
Le hub Edge, qui fait partie du runtime Edge, est optimisé pour les performances par défaut et tente d’allouer de grandes quantités de mémoire. Cette optimisation n’est pas idéale pour les appareils de périphérie limités et peut entraîner des problèmes de stabilité.

### <a name="resolution"></a>Résolution :
Pour le hub de périphérie, affectez la valeur **false** à une variable d’environnement **OptimizeForPerformance**. Il existe deux façons d'effectuer cette opération :

Dans l’interface utilisateur : 

Dans le portail à partir de *Détails de l’appareil*->*Définir des modules*->*Configurer les paramètres avancés du runtime Edge*, créez une variable d’environnement appelée *OptimizeForPerformance* avec la valeur *false* pour *Hub Edge*.

![optimizeforperformance](./media/troubleshoot/OptimizeForPerformanceFalse.png)

**OR**

Dans le manifeste de déploiement :

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Impossible d’obtenir les journaux de démon IoT Edge sur Windows
Si vous obtenez une EventLogException lors de l’utilisation de `Get-WinEvent` sur Windows, vérifiez les entrées de Registre.

### <a name="root-cause"></a>Cause racine
La commande PowerShell `Get-WinEvent` repose sur la présence d’une entrée de Registre pour trouver les journaux d’après un `ProviderName` spécifique.

### <a name="resolution"></a>Résolution :
Définissez une entrée de Registre pour le démon IoT Edge. Créez un fichier **iotedge.reg** avec le contenu suivant, puis importez-le dans le Registre Windows en double-cliquant dessus ou en utilisant la commande `reg import iotedge.reg` :

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Le module IoT Edge ne parvient pas à envoyer de message à l’edgeHub avec l’erreur 404

Un module IoT Edge personnalisé ne parvient pas à envoyer de message à l’edgeHub avec l’erreur `Module not found` 404. Le démon IoT Edge imprime le message suivant dans les journaux : 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Cause racine
Pour des raisons de sécurité, le démon IoT Edge applique l’identification du processus à tous les modules se connectant à l’edgeHub. Il vérifie que tous les messages envoyés par un module proviennent de l’ID de processus principal du module. Si un message est envoyé par un module depuis un ID de processus différent de celui qui a été établi initialement, il rejette le message avec un message d’erreur 404.

### <a name="resolution"></a>Résolution :
Assurez-vous que le même ID de processus est bien toujours utilisé par le module IoT Edge personnalisé pour envoyer des messages au hub de périphérie. Par exemple, veillez à utiliser `ENTRYPOINT` plutôt que la commande `CMD` dans votre fichier Docker, car `CMD` va générer un ID de processus pour le module et un autre pour la commande bash exécutant le programme principal, tandis que `ENTRYPOINT` génère un ID de processus unique.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Règles de configuration du pare-feu et des ports pour le déploiement d’IoT Edge
Azure IoT Edge permet la communication entre un serveur Edge local et le cloud Azure au moyen des protocoles IoT Hub pris en charge (voir [Choisir un protocole de communication](../iot-hub/iot-hub-devguide-protocols.md)). Pour renforcer la sécurité, les canaux de communication entre Azure IoT Edge et Azure IoT Hub sont toujours configurés pour être sortants. Cette configuration est basée sur le [modèle de communication assistée par des services](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), ce qui réduit la surface d’attaque qu’une entité malveillante pourrait explorer. Les communications entrantes sont uniquement requises pour des scénarios spécifiques où Azure IoT Hub a besoin d’envoyer (push) des messages à l’appareil Azure IoT Edge. Les messages cloud-à-appareil sont protégés à l’aide de canaux TLS sécurisés, protection qui peut être renforcée à l’aide de certificats X.509 et de modules d’appareil TPM. Le Gestionnaire de sécurité Azure IoT Edge régit la façon dont cette communication peut être établie (voir [Gestionnaire de sécurité IoT Edge](../iot-edge/iot-edge-security-manager.md)).

Bien que IoT Edge assure une configuration améliorée pour la sécurisation du runtime Azure IoT Edge et des modules déployés, il dépend toujours de la configuration du réseau et de l’ordinateur sous-jacents. Il est donc indispensable de vérifier que les règles de pare-feu et de réseau sont bien configurées pour assurer la sécurisation de la communication Edge vers Cloud. Suivez la procédure suivante pour configurer les règles de pare-feu pour les serveurs sous-jacents hébergeant le runtime Azure IoT Edge :

|Protocole|Port|Entrant|Sortant|Assistance|
|--|--|--|--|--|
|MQTT|8883|BLOQUÉ (par défaut)|BLOQUÉ (par défaut)|<ul> <li>Configurez Sortant sur Ouvert lorsque vous utilisez MQTT comme protocole de communication.<li>1883 pour MQTT n’est pas pris en charge par IoT Edge. <li>Les connexions entrantes doivent être bloquées.</ul>|
|AMQP|5671|BLOQUÉ (par défaut)|OUVERT (par défaut)|<ul> <li>Protocole de communication par défaut pour IoT Edge. <li> Doit être configuré sur Ouvert si Azure IoT Edge n’est pas configuré pour les autres protocoles pris en charge ou si AMQP est le protocole de communication souhaité.<li>5672 pour AMQP n’est pas pris en charge par IoT Edge.<li>Bloquez ce port quand Azure IoT Edge utilise un autre protocole IoT Hub pris en charge.<li>Les connexions entrantes doivent être bloquées.</ul></ul>|
|HTTPS|443|BLOQUÉ (par défaut)|OUVERT (par défaut)|<ul> <li>Configurez la connexion sortante pour être Ouverte sur le port 443 pour le provisionnement d’IoT Edge. Cette configuration est requise en cas d’utilisation de scripts manuels ou du service Azure IoT Device Provisioning. <li>La connexion entrante ne peut être Ouverte que dans certaines situations uniquement : <ul> <li>  Si vous disposez d’une passerelle transparente avec des appareils de nœud terminal pouvant envoyer des requêtes de méthode. Dans ce cas, le port 443 n’a pas besoin d’être ouvert aux réseaux externes pour se connecter à IoTHub ou fournir des services IoTHub via Azure IoT Edge. La règle entrante peut donc être limitée uniquement à l’ouverture du trafic entrant en provenance du réseau interne. <li> Pour les scénarios Client vers Appareil (C2D).</ul><li>80 pour HTTP n’est pas pris en charge par IoT Edge.<li>Si les protocoles non-HTTP (par exemple, AMQP ou MQTT) ne peuvent pas être configurés dans l’entreprise, les messages peuvent être envoyés sur WebSockets. Dans ce cas, le port 443 sera utilisé pour la communication WebSocket.</ul>|


## <a name="next-steps"></a>Étapes suivantes
Vous pensez que vous avez trouvé un bogue dans la plateforme IoT Edge ? [Soumettez un problème](https://github.com/Azure/iotedge/issues) afin que nous puissions poursuivre les améliorations. 

Si vous avez d'autres questions, créez une [Support request](https://portal.azure.com/#create/Microsoft.Support) pour obtenir de l'aide. 

