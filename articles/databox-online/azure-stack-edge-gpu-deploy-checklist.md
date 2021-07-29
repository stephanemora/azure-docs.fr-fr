---
title: Liste de contrôle préalable au déploiement d’un processeur GPU Azure Stack Edge Pro | Microsoft Docs
description: Cet article décrit les informations qui peuvent être collectées avant de déployer un processeur GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/07/2021
ms.author: alkohli
ms.openlocfilehash: 9bb7f552fccf4b6f58def2046b80473a57e6116f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756380"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Liste de contrôle du déploiement d’un processeur GPU Azure Stack Edge Pro  

Cet article décrit les informations qui peuvent être collectées avant le déploiement proprement dit d’un appareil Azure Stack Edge Pro. 

Suivez la liste de contrôle ci-dessous pour vérifier que vous disposez de ces informations après avoir passé commande d’un appareil Azure Stack Edge Pro et avant de le recevoir. 

## <a name="deployment-checklist"></a>Liste de vérification de déploiement 

| Étape                             | Paramètre                                                                                                                                                                                                                           | Détails                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestion des appareils               | <li>Abonnement Azure</li><li>Fournisseurs de ressources inscrits</li><li>Compte de Stockage Azure</li>|<li>Activé pour Azure Stack Edge Pro/Data Box Gateway, accès Propriétaire ou Contributeur.</li><li>Sur le portail Azure, accédez à **Accueil > Abonnements > Votre abonnement > Fournisseurs de ressources**. Recherchez `Microsoft.DataBoxEdge` et inscrivez-vous. Répétez cette opération pour `Microsoft.Devices` si vous déployez des charges de travail IoT.</li><li>Informations d’identification d’accès requises</li> |
| Installation des périphériques               | Câbles d’alimentation dans le package. <br>Pour les États-Unis, un câble SVE 18/3 homologué 125 V et 15 A avec un connecteur NEMA 5-15P vers C13 (entrée vers sortie) est livré. | Pour plus d’informations, consultez la liste des [Cordons d’alimentation pris en charge selon les pays](azure-stack-edge-technical-specifications-power-cords-regional.md).  |
|                                   | <li>Au moins un câble réseau 1-GbE RJ-45 pour le port 1  </li><li> Au moins un câble cuivre 25/10-GbE SFP+ pour le port 3, 4, 5 ou 6</li>| Il revient au client de se procurer ces câbles.<br>Pour obtenir la liste complète des câbles réseau, commutateurs et transmetteurs pris en charge pour les cartes réseau d’appareil à partir de Cavium, consultez le document [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)<br>Pour obtenir la liste complète des câbles et des modules pris en charge pour 25 GbE et 10 GbE à partir de Mellanox, consultez [Mellanox Dual Port 25G ConnectX- produits compatibles avec les cartes réseau 4 canaux](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Première connexion de l’appareil      | <li>Ordinateur portable dont les paramètres IPv4 peuvent être modifiés. Cet ordinateur portable se connecte au Port 1 via un commutateur ou un adaptateur USB vers Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Connexion de l’appareil                       | Mot de passe de l’administrateur de l’appareil, compris entre 8 et 16 caractères, dont trois des types de caractère suivants : majuscules, minuscules, chiffres et caractères spéciaux.                                            | Le mot de passe par défaut, *Password1*, expire à la première connexion.                                                     |
| Paramètres réseau                  | L’appareil est fourni avec des ports réseau 2 × 1 GbE, 4 × 25 GbE. <li>Le port 1 n’est utilisé que pour configurer les paramètres de gestion. Il est possible de connecter et de configurer un ou plusieurs ports de données. </li><li> Il faut qu’au moins une interface réseau de données entre le port 2 et le port 6 soit connectée à Internet (avec connectivité à Azure).</li><li> Prise en charge du protocole DHCP et de la configuration IPv4 statique. | L’adresse IP, le serveur DNS et la passerelle par défaut sont nécessaires pour la configuration IPv4 statique.   |
| Paramètres réseau de calcul     | <li>Deux adresses IP libres, statiques et contiguës sont requises pour les nœuds Kubernetes, ainsi qu'une  adresse IP statique pour le service IoT Edge.</li><li>Une adresse IP supplémentaire est requise pour chaque service ou module supplémentaire déployé.</li>| Seule la configuration IPv4 statique est prise en charge.|
| (Facultatif) Paramètres de proxy web     | <li>Adresse IP/nom de domaine complet du serveur proxy web, port </li><li>Nom d'utilisateur du proxy web, mot de passe</li> |  |
| Paramètre de pare-feu et de port        | Si vous utilisez un pare-feu, vérifiez que les [modèles et ports d'URL listés](azure-stack-edge-system-requirements.md#networking-port-requirements) sont autorisés pour les adresses IP des appareils. |  |
| (Recommandé) Paramètres d’heure       | Configurer le fuseau horaire, le serveur NTP principal et le serveur NTP secondaire. | Configurez le serveur NTP principal et secondaire sur le réseau local.<br>Si le serveur local n’est pas disponible, des serveurs NTP publics peuvent être configurés.                                                    |
| (Facultatif) Paramètres du serveur de mise à jour | <li>Exiger l’adresse IP du serveur de mise à jour sur le réseau local, chemin du serveur WSUS. </li> | Par défaut, c’est le serveur Windows Update public qui est utilisé.|
| Paramètres de l’appareil | <li>Nom de domaine complet (FQDN) de l'appareil </li><li>Domaine DNS</li> | |
| (Facultatif) Certificats  | Pour tester des charges de travail hors production, utilisez l'[option Générer des certificats](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). <br><br> Si vous apportez vos propres certificats comprenant la ou les chaînes de signature, [ajoutez les certificats](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) au format approprié.| Ne configurez des certificats que si vous changez le nom de l’appareil et/ou le domaine DNS. |
| Activation  | Exiger une clé d’activation de la ressource Azure Stack Edge Pro/Data Box Gateway.    | Une fois générée, la clé expire au bout de trois jours. |

<!--
| (Optional) MAC Address            | If MAC address needs to be on the allowed list, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Étapes suivantes

Préparez le déploiement de votre [appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).
