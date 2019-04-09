---
title: Présentation de la gestion des certificats OPC UA Azure IoT | Microsoft Docs
description: Vue d’ensemble d’OPC Vault
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759731"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Présentation de la gestion des certificats OPC (Open Platform Communications) UA Azure IoT

La gestion des certificats OPC UA Azure IoT, également appelée OPC Vault, est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article décrit les cas d’utilisation simples d’OPC Vault.

## <a name="certificate-management"></a>Gestion des certificats

Par exemple, une entreprise de fabrication doit connecter sa machine serveur OPC UA à son application cliente nouvellement générée. Quand cette entreprise effectue l’accès initial de la machine serveur, un message d’erreur est immédiatement visible sur l’application serveur OPC UA pour indiquer que l’application cliente n’est pas sécurisée. Ce mécanisme est intégré à la machine serveur OPC UA pour empêcher tout accès non autorisé à l’application, prévenant un piratage vicieux au niveau de l’atelier.

## <a name="application-security-management"></a>Gestion de la sécurité des applications
Un professionnel de la sécurité utilise le microservice OPC Vault pour permettre facilement au serveur OPC UA de communiquer avec n’importe quelle application cliente, étant donné qu’OPC Vault a toutes les fonctions de registre de certificat, de stockage et de gestion du cycle de vie. Le serveur OPC UA étant connecté de manière sécurisée, il peut communiquer avec l’application cliente nouvellement générée.

## <a name="the-complete-opc-vault-architecture"></a>Architecture d’OPC Vault complète
Le diagramme suivant illustre l’architecture d’OPC Vault complète.

![Architecture d’OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)