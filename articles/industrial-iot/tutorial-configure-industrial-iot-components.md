---
title: Configurer les composants Azure Industrial IoT
description: Dans ce tutoriel, vous allez apprendre à changer les valeurs par défaut de la configuration.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787228"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Tutoriel : Configurer les composants Industrial IoT

Le script de déploiement configure automatiquement tous les composants pour qu’ils fonctionnent les uns avec les autres en utilisant les valeurs par défaut. Toutefois, les paramètres des valeurs par défaut peuvent être changés pour qu’ils répondent à vos besoins.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Personnaliser la configuration des composants


Voici quelques-uns des paramètres de personnalisation les plus pertinents pour les composants :
* IoT Hub
    * Réseau → accès public : configurer l’accès à Internet, par exemple, les filtres IP
    * Réseau → Connexions de points de terminaison privés : créer un point de terminaison qui n’est pas accessible via Internet et qui peut être consommé en interne par d’autres services Azure ou des appareils locaux (par exemple, par le biais d’une connexion VPN)
    * IoT Edge : gérer la configuration des appareils périphériques connectés aux serveurs OPC UA 
* Cosmos DB
    * Répliquer les données dans le monde entier : configurer la redondance des données
    * Pare-feu et réseaux virtuels : configurer l’accès à Internet et au réseau virtuel ainsi que les filtres IP
    * Connexions de points de terminaison privés : créer un point de terminaison qui n’est pas accessible via Internet 
* Key Vault
    * Secrets : gérer les paramètres de plateforme
    * Stratégies d’accès : gérer les applications et les utilisateurs qui peuvent accéder aux données du coffre de clés et les opérations (par exemple, lecture, écriture, listage, suppression) qu’ils sont autorisés à effectuer sur le réseau, le pare-feu, le réseau virtuel et les points de terminaison privés
* Azure Active Directory (AAD)→Inscriptions d’applications
    * <APP_NAME>-Web → authentification : gérer les URI de réponse, qui est la liste des URI qui peuvent être utilisés comme pages d’accueil une fois l’authentification réussie. Le script de déploiement n’est peut-être pas en mesure de le configurer automatiquement dans certains scénarios, tels que l’absence de droits d’administrateur AAD. Vous pouvez ajouter ou modifier des URI lors du changement du nom d’hôte de l’application web, par exemple, le numéro de port utilisé par l’hôte local pour le débogage
* App Service
    * Configuration : gérer les variables d’environnement qui contrôlent les services ou l’interface utilisateur
* Machine virtuelle
    * Réseau : configurer les réseaux et les règles de pare-feu pris en charge
    * Console série : accéder en mode SSH pour obtenir des insights ou à des fins de débogage, obtenir les informations d’identification à partir de la sortie du script de déploiement ou réinitialiser le mot de passe
* IoT Hub → IoT Edge
    * Gérer les identités des appareils IoT Edge qui peuvent accéder au hub, configurer les modules installés et la configuration qu’ils utilisent, par exemple les paramètres d’encodage pour OPC Publisher
* IoT Hub → IoT Edge → \<DEVICE> → Définir des modules → OpcPublisher (pour l’exploitation d’OPC Publisher en mode autonome uniquement)


## <a name="configuration-options"></a>Options de configuration

|Option de configuration (nom abrégé/nom complet)    |    Description   |
|----------------------------------------------|------------------|
pf/publishfile |Nom de fichier pour configurer les nœuds à publier. Si cette option est spécifiée, OPC Publisher est placé en mode autonome.
lf/logfile |Nom du fichier journal à utiliser.
ll/loglevel |Niveau de journal à utiliser (valeurs autorisées : fatal, error, warn, info, debug, verbose).
me/messageencoding |Encodage de la messagerie pour les messages sortants. Valeurs autorisées : Json, Uadp
mm/messagingmode |Mode de messagerie pour les messages sortants. Valeurs autorisées : PubSub, Samples
fm/fullfeaturedmessage |Mode complet pour les messages (tous les champs renseignés). La valeur par défaut est « true », pour la compatibilité héritée, utilisez « false »
aa/autoaccept |Le serveur de publication a approuvé tous les serveurs auxquels il est connecté.
bs/batchsize |Nombre de messages de modification de données OPC UA à mettre en cache pour le traitement par lot.
si/iothubsendinterval |Intervalle de traitement par lot du déclencheur, en secondes.
ms/iothubmessagesize |Taille maximale du message (IoT D2C).
om/maxoutgressmessages |Taille maximale de la mémoire tampon de sortie de message (IoT D2C).
di/diagnosticsinterval |Affiche les informations de diagnostic du serveur de publication à l’intervalle spécifié en secondes (informations de niveau de journal requises). -1 désactive le journal de diagnostic distant et la sortie du diagnostic
lt/logflugtimespan |Intervalle de temps en secondes pendant lequel le fichier journal doit être vidé.
ih/iothubprotocol |Protocole à utiliser pour la communication avec le hub. Valeurs autorisées : AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, Amqp, Mqtt, Tcp, Websocket, Any
hb/heartbeatinterval |Le serveur de publication utilise cette valeur par défaut en secondes pour le paramètre de l’intervalle de pulsation des nœuds dépourvus de paramètre d’intervalle de pulsation.
ot/operationtimeout |Délai d’expiration de l’opération du client du serveur de publication OPC UA en millisecondes.
ol/opcmaxstringlen |Longueur maximale d’une chaîne qu’OPC peut transmettre/recevoir.
oi/opcsamplinginterval |Valeur par défaut, en millisecondes, pour demander aux serveurs d’échantillonner des valeurs
op/opcpublishinginterval |Valeur par défaut, en millisecondes, pour le paramètre de l’intervalle de publication des abonnements sur le serveur OPC UA.
ct/createsessiontimeout |Intervalle, en secondes, auquel le serveur de publication envoie des messages Keep Alive aux serveurs OPC sur les points de terminaison auxquels il est connecté.
kt/keepalivethresholt |Spécifiez le nombre de paquets Keep Alive qu’un serveur peut manquer avant que la session ne soit déconnectée.
tm/trustmyself |Le certificat du serveur de publication est automatiquement placé dans le magasin approuvé.
at/appcertstoretype |Le type de magasin de certificats de l’application (valeurs autorisées : Directory, X509Store).


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à changer les valeurs par défaut de la configuration, vous pouvez 

> [!div class="nextstepaction"]
> [Extraire les données IIoT dans ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualiser et analyser les données avec Time Series Insights](tutorial-visualize-data-time-series-insights.md)
