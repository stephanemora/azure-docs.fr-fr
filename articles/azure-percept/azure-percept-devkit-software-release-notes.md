---
title: Notes de publication des versions de logiciel pour Azure Percept DK
description: Informations sur les modifications apportées au logiciel Azure Percept DK.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: f957ad88d89b9d31ab8e073214e765dbbdb13094
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224931"
---
# <a name="azure-percept-dk-software-release-notes"></a>Notes de publication des versions de logiciel pour Azure Percept DK

Cette page fournit des informations sur les modifications et les correctifs pour chaque version du microprogramme et du système d’exploitation Azure Percept DK.

Pour télécharger les images de mise à jour, consultez [Versions du logiciel Azure Percept DK pour mise à jour via câble USB](./software-releases-usb-cable-updates.md) ou [Versions du logiciel Azure Percept DK pour mise à jour OTA](./software-releases-over-the-air-updates.md).

## <a name="july-2107-release"></a>Version de juillet (2107)

> [!IMPORTANT]
> En raison d’une modification de la signature du code, le package OTA (Over-The-Air) pour cette version n’est compatible qu’avec Azure Percept DK version 2106. Pour les utilisateurs d’Azure Percept DK qui disposent actuellement d’une version plus ancienne du logiciel, Microsoft recommande d’effectuer une mise à jour via un câble USB ou OTA vers la version 2106 avant de procéder à la mise à jour vers la version 2107.

- Wi-Fi :
  - Renforcement de la sécurité pour s’assurer que le point d’accès Wi-Fi s’arrête une fois l’installation terminée.
  - Correction d’un problème qui avait pour effet que l’action du bouton **Installer** sur le kit de développement pouvait désynchroniser le point d’accès Wi-Fi du kit de développement du service web de l’expérience d’installation.
  - Amélioration des règles IPTables de point d’accès Wi-Fi afin de les rendre plus résilientes et de supprimer celles qui sont superflues.
  - Résolution d’un problème qui avait pour effet que, lorsque plusieurs réseaux Wi-Fi étaient connectés, ils n’étaient pas correctement hiérarchisés.
- Expérience d’installation :
  - Ajout de la localisation pour les régions prises en charge et mise à jour du texte pour une meilleure lisibilité.
  - Résolution d’un problème qui avait pour effet que l’expérience d’installation bloquait parfois sur une page de chargement.
- Mise en réseau générale :
  - Résolution de problèmes qui avaient pour effet qu’IPv6 n’obtenait pas de bail DHCP valide.
- Système d’exploitation :
  - Correctifs de sécurité.

## <a name="june-2106-release"></a>Version de juin (2106)

- Mécanisme de vérification d’image mis à jour pour l’agent OTA.
- Améliorations de l’interface utilisateur et corrections de bogues pour l’expérience d’installation.

## <a name="may-2105-release"></a>Version de mai (2105)

- Correctifs de sécurité pour le système d’exploitation CBL-Mariner.

## <a name="april-2104-release"></a>Version d’avril (2104)

- Correction d’un problème de rotation des journaux qui pouvait entraîner la saturation du stockage Azure Percept DK.
- Activation de l’approvisionnement d’Azure basé sur TMP durant l’expérience d’installation.
- Ajout d’un délai d’expiration automatique pour l’expérience d’installation et le point d’accès Wi-Fi. Après 30 minutes ou à l’issue de l’expérience d’installation.
- Modification du SSID de point d’accès Wi-Fi « **scz-[xxx]**  » en « **apd-[xxx]**  ».

## <a name="next-steps"></a>Étapes suivantes

- [Comment déterminer votre stratégie de mise à jour](./how-to-determine-your-update-strategy.md)
