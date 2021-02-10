---
title: Configurer la haute disponibilité
description: Augmentez la résilience de votre déploiement de Defender pour IoT en installant une appliance à haute disponibilité de la console de gestion locale. Les déploiements à haute disponibilité garantissent que vos capteurs gérés envoient en permanence des rapports à la console de gestion locale active.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: eb5f2c6293042f44de5e3c061c6d379bee6e5b06
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820583"
---
# <a name="about-high-availability"></a>À propos de la haute disponibilité

Augmentez la résilience de votre déploiement de Defender pour IoT en installant une appliance à haute disponibilité de la console de gestion locale. Les déploiements à haute disponibilité garantissent que vos capteurs gérés envoient en permanence des rapports à la console de gestion locale active.

Ce déploiement est implémenté avec une paire de la console de gestion locale qui comprend une appliance principale et secondaire.

## <a name="about-primary-and-secondary-communication"></a>À propos des communications principales et secondaires

Quand une console de gestion locale principale et une secondaire sont associées :

- Un certificat SSL de la console de gestion locale est appliqué pour créer une connexion sécurisée entre les appliances principale et secondaire. Le SSL peut être le certificat auto-signé installé par défaut ou un certificat installé par le client.

- Les données de la console de gestion locale principale sont automatiquement sauvegardées sur la console de gestion locale secondaire toutes les 10 minutes. Les configurations de la console de gestion locale et les données des appareils sont sauvegardées. Les fichiers PCAP et les journaux ne sont pas inclus dans la sauvegarde. Vous pouvez sauvegarder et restaurer manuellement les fichiers PCAP et les journaux.

- Le configuration principale sur la console de gestion est dupliquée sur la console secondaire. Par exemple, les paramètres système. Si ces paramètres sont mis à jour sur la console principale, ils sont également mis à jour sur la console secondaire.

- Avant la licence de la console secondaire expire, vous devez définir cette dernière comme console principale afin de mettre à jour la licence.

## <a name="about-failover-and-failback"></a>À propos du basculement et de la restauration automatique

Si un capteur ne peut pas se connecter à la console de gestion locale principale, il se connecte automatiquement à la console secondaire. Votre système sera pris en charge par les consoles principale et secondaire simultanément, si moins de la moitié des capteurs communiquent avec la console secondaire. La console secondaire prend le relais lorsque plus de la moitié des capteurs communiquent avec elle. Le basculement de la console principale vers celle secondaire prend environ trois minutes. Pendant le basculement, la console de gestion locale principale se fige. Lorsque cela se produit, vous pouvez vous connecter à la console secondaire en utilisant les mêmes informations d’identification de connexion.

Pendant le basculement, les capteurs continuent à essayer de communiquer avec l’appliance principale. Lorsque plus de la moitié des capteurs gérés réussissent à communiquer avec la console principale, la console principale est restaurée. Le message suivant s’affiche sur la console secondaire lorsque la console principale est restaurée.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Capture d’écran d’un message qui s’affiche sur la console secondaire lorsque la console principale est restaurée.":::

Reconnectez-vous à l’appliance principale après la redirection.

## <a name="high-availability-setup-overview"></a>Présentation de la configuration de la haute disponibilité

Les procédures d’installation et de configuration sont effectuées en quatre étapes principales :

1. Installez une appliance principale de la console de gestion locale. 

2. Configurez l’appliance principale de la console de gestion locale. Par exemple, les paramètres de sauvegarde planifiée, les paramètres VLAN. Pour plus d’informations, consultez le guide de l’utilisateur de la console de gestion locale. Tous les paramètres sont automatiquement appliqués à l’appliance secondaire après l’appariement.

3. Installez une appliance secondaire de la console de gestion locale. Pour plus d’informations, consultez [À propose de l’installation de Defender pour IoT](how-to-install-software.md).

4. Appairez les appliances principale et secondaire de la console de gestion locale comme décrit [ici](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). La console de gestion locale principale doit gérer au moins deux capteurs pour procéder à l’installation.

## <a name="high-availability-requirements"></a>Conditions requises pour la haute disponibilité

Vérifiez que vous respectez les conditions requises suivantes pour la haute disponibilité :

- Configuration requise des certificats

- Configurations matérielle et logicielle requises

- Conditions requises pour l’accès au réseau

### <a name="software-and-hardware-requirements"></a>Configurations matérielle et logicielle requises

- Les appliances principale et secondaire de la console de gestion locale doivent exécuter des modèles de matériel et des versions de logiciels identiques.

- Le système de haute disponibilité peut être configuré par les utilisateurs Defender pour IoT uniquement, à l’aide d’outils CLI.

### <a name="network-access-requirements"></a>Conditions requises pour l’accès au réseau

Vous devez vérifier que la stratégie de sécurité de votre organisation vous permet d’accéder aux services suivants sur les consoles de gestion locale principale et secondaire. Ces services permettent également la connexion entre les capteurs et la console de gestion locale secondaire :

|Port|Service|Description|
|----|-------|-----------|
|**443 ou TCP**|HTTPS|Octroie l’accès à la console web de la console de gestion locale.|
|**22 ou TCP**|SSH|Synchronise les données entre les appliances principale et secondaire de la console de gestion locale.|
|**123 ou UDP**|NTP| Synchronisation temporelle NTP de la console de gestion locale. Vérifiez que les appliances actives et passives sont définies sur le même fuseau horaire.|

## <a name="create-the-primary-and-secondary-pair"></a>Créer la paire principale et secondaire

Vérifiez que les appliances principale et secondaire de la console de gestion locale sont sous tension avant de commencer la procédure.  

### <a name="on-the-primary"></a>Sur l’appliance principale

1. Connectez-vous à l’interface CLI en tant qu’utilisateur Defender pour IoT.

2. Exécutez la commande suivante sur l’appliance principale :

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>Dans ce document, la console de gestion locale principale est appelée « la principale » et l’agent est désigné sous le terme « la secondaire ».

3. Entrez l’adresse IP de l’appliance secondaire dans le champ ```<Secondary ip>```, puis sélectionnez Entrée. L’adresse IP est ensuite validée et le certificat SSL est téléchargé sur la principale. La saisie de l’adresse IP associe également les capteurs à l’appliance secondaire.

4. Exécutez la commande suivante sur la principale pour vérifier que le certificat est correctement installé :

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Exécutez la commande suivante sur la principale. **Ne s’exécute pas avec sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Cela permet la connexion entre les appliances principale et secondaire à des fins de sauvegarde et de restauration entre elles.

6. Entrez l’adresse IP de la secondaire, puis sélectionnez Entrée.

### <a name="on-the-secondary"></a>Sur l’appliance secondaire

1. Connectez-vous à l’interface CLI en tant qu’utilisateur Defender pour IoT.

2. Exécutez la commande suivante sur la secondaire. **Ne s’exécute pas avec sudo** :

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Cela permet la connexion entre les appliances principale et secondaire à des fins de sauvegarde et de restauration entre elles.

3. Entrez l’adresse IP de la principale et appuyez sur Entrée.

### <a name="track-high-availability-activity"></a>Suivre l’activité de haute disponibilité

Les journaux des applications de base peuvent être exportés vers l’équipe du support technique de Defender pour IoT pour traiter tout problème de haute disponibilité.  

Pour accéder aux journaux principaux :

1. Sélectionnez **Exporter** dans la fenêtre **Paramètres système**.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Mettre à jour la console de gestion locale avec une haute disponibilité

Effectuez la mise à jour de la haute disponibilité dans l’ordre suivant. Assurez-vous que chaque étape est terminée avant de commencer une nouvelle étape.

Pour mettre à jour avec une haute disponibilité :

1. Mettez à jour la console de gestion locale principale.

2. Mettez à jour la console de gestion locale secondaire.

3. Mettez à jour les capteurs.

## <a name="see-also"></a>Voir aussi

[Activer et configurer votre console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md)