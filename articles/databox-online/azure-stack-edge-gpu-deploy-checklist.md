---
title: Liste de contrôle préalable au déploiement d’un processeur GPU Azure Stack Edge Pro | Microsoft Docs
description: Cet article décrit les informations qui peuvent être collectées avant de déployer un processeur GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 82751ea821bb1edfea5dfb353cbb3cdc51fe59d3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903011"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Liste de contrôle du déploiement d’un processeur GPU Azure Stack Edge Pro  

Cet article décrit les informations qui peuvent être collectées avant le déploiement proprement dit d’un appareil Azure Stack Edge Pro. 

Suivez la liste de contrôle ci-dessous pour vérifier que vous disposez de ces informations après avoir passé commande d’un appareil Azure Stack Edge Pro et avant de le recevoir. 

## <a name="deployment-checklist"></a>Liste de vérification de déploiement 

| Étape                             | Paramètre                                                                                                                                                                                                                           | Détails                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestion des appareils               | <li>Abonnement Microsoft Azure</li><li>API Graph Azure Active Directory</li><li>Compte Stockage Microsoft Azure</li>|<li>Activé pour Azure Stack Edge Pro/Data Box Gateway, autorisations de contributeur</li><li>Accès administrateur ou utilisateur</li><li>Informations d’identification d’accès requises</li> |
| Installation des périphériques               | Câbles d’alimentation dans le package. <br>Pour les États-Unis, un câble SVE 18/3 homologué 125 V et 15 A avec un connecteur NEMA 5-15P vers C13 (entrée vers sortie) est livré.                                                                                                                                                                                                          | Fourni avec l’appareil.<br>Pour plus d’informations, consultez la liste des [Cordons d’alimentation pris en charge selon les pays](azure-stack-edge-technical-specifications-power-cords-regional.md).                                                                                        |
|                                   | <li>Au moins un câble réseau 1-GbE RJ-45 pour le port 1  </li><li> Au moins un câble cuivre 25-GbE SFP+ pour le port 3, 4, 5 ou 6</li>| Il revient au client de se procurer ces câbles.<br>Pour obtenir la liste complète des câbles réseau, des commutateurs et des transmetteurs pris en charge pour les cartes réseau de l’appareil, consultez [Matrice d’interopérabilité de la série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) et [Produits compatibles avec la carte réseau Mellanox double port 25G ConnectX-4 canaux](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Première connexion de l’appareil      | Le port 1 a une adresse IP fixe (192.168.100.10/24) pour la connexion initiale. <li>Exiger un ordinateur portable pour la connexion directe au port 1, avec une adresse IP sur le réseau 192.168.100.0/24.</li><li> Utiliser l’interface utilisateur locale de l’appareil à l’adresse `https://192.168.100.10` pour la configuration avancée.</li><li> Un commutateur 1 GbE au minimum doit être utilisé pour l’appareil une fois l’installation initiale effectuée. L’interface utilisateur web locale n’est pas accessible si le commutateur connecté n’offre pas un débit d’au moins 1 GbE.</li>|                                                                                                                   |
| Ouverture de session sur l’appareil                      | Le mot de passe d’administrateur de l'appareil doit être compris entre 8 et 16 caractères. <br>Il doit contenir trois des types de caractères suivants : majuscule, minuscule, chiffre et caractère spécial.                                            | Le mot de passe par défaut, *Password1*, expire à la première connexion.                                                     |
| Paramètres réseau                  | L’appareil est fourni avec des ports réseau 2 × 1 GbE, 4 × 25 GbE. <li>Le port 1 n’est utilisé que pour configurer les paramètres de gestion. Il est possible de connecter et de configurer un ou plusieurs ports de données. </li><li> Il faut qu’au moins une interface réseau de données entre le port 2 et le port 6 soit connectée à Internet (avec connectivité à Azure).</li><li> Les paramètres IP prennent en charge la configuration DHCP/IPv4 statique. | L’adresse IP, le serveur DNS et la passerelle par défaut sont nécessaires pour la configuration IPv4 statique.                                                                                                                  |
| Paramètres réseau de calcul     | <li>Exiger deux adresses IP publiques statiques pour les nœuds Kubernetes et au moins une adresse IP statique pour permettre au service Azure Stack Edge Pro Hub d’accéder aux modules de calcul.</li><li>Exiger une adresse IP par service ou conteneur supplémentaire qui doit être accessible de l’extérieur du cluster Kubernetes.</li>                                                                                                                       | Seule la configuration IPv4 statique est prise en charge.                                                                      |
| (Facultatif) Paramètres de proxy web     | <li>Adresse IP/nom de domaine complet du serveur proxy web, port </li><li>Nom d'utilisateur du proxy web, mot de passe</li>                                                                                                                                                                                                    | Actuellement non pris en charge avec l’installation du calcul.                                                                     |
| Paramètre de pare-feu et de port        | Utilisez les [modèles et ports d’URL listés](azure-stack-edge-system-requirements.md#networking-port-requirements) à autoriser pour les adresses IP des appareils.                                                                                                                                                  |                                                                                                                   |
| (Facultatif) Adresse MAC            | Si l’adresse MAC doit être sur liste verte, récupérez l’adresse du port connecté à partir de l’interface utilisateur locale de l’appareil. |                                                                                                                   |
| (Facultatif) Port du commutateur réseau    | L’appareil héberge des machines virtuelles Hyper-V pour le calcul. Certaines configurations de port de commutateur réseau ne prennent pas en charge ces installations par défaut.                                                                                                        |                                                                                                                   |
| (Recommandé) Paramètres d’heure       | Configurer le fuseau horaire, le serveur NTP principal et le serveur NTP secondaire.                                                                                                                                                                    | Configurez le serveur NTP principal et secondaire sur le réseau local.<br>Si le serveur local n’est pas disponible, des serveurs NTP publics peuvent être configurés.                                                    |
| (Facultatif) Paramètres du serveur de mise à jour | <li>Exiger l’adresse IP du serveur de mise à jour sur le réseau local, chemin du serveur WSUS. </li> | Par défaut, c’est le serveur Windows Update public qui est utilisé.|
| Paramètres de l’appareil                   | <li>Nom de l’appareil inscrit au sein de l’organisation DNS </li><li>Domaine DNS</li> |                                                                                                                   |
| (Facultatif) Certificats                      | Utiliser les certificats générés par l’appareil. <br><br> Si vous disposez de vos propres certificats, il vous faut : <li>un certificat de signature racine de approuvé au format DER avec *.cer* ; </li><li>des certificats de point de terminaison au format *PFX*.</li>|Parmi les certificats de point de terminaison figurent Azure Resource Manager, le Stockage Blob et l’interface utilisateur Web locale.                                                                                                                   |
| Activation                        | Exiger une clé d’activation de la ressource Azure Stack Edge Pro/Data Box Gateway.                                                                                                                                                       | Une fois générée, la clé expire au bout de trois jours.                                                                        |


## <a name="next-steps"></a>Étapes suivantes

Préparez le déploiement de votre [appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).



