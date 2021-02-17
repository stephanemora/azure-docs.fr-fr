---
title: Fonctionnalités de l’infrastructure d’interface utilisateur d’Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Découvrir les fonctionnalités de l’infrastructure d’interface utilisateur
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 51caf1e73cdeda5862e033a05878f35ddb6ff8dc
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539708"
---
# <a name="ui-framework-capabilities"></a>Fonctionnalités de l’infrastructure d’interface utilisateur

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

L’infrastructure d’interface utilisateur d’Azure Communication Services vous permet de créer des expériences de communication à partir d’un ensemble de composants réutilisables. Il existe deux types de composant : Les composants **de base** sont les plus élémentaires de votre expérience d’interface utilisateur ; ces composants de base peuvent être combinés pour former un composant **composite**.

## <a name="ui-framework-composite-components"></a>Composants composites de l’infrastructure d’interface utilisateur

| Composite               | Description                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Composite d’appel de groupe | Expérience d’appel sortant vocal et vidéo légère pour les appels Azure Communication Services utilisant des ressources de conception Fluent UI. Prend en charge l’appel de groupe avec un ID de groupe Azure Communication Services. Le composite permet l’utilisation d’un appel un-à-un en référençant une identité Azure Communication Services ou un numéro de téléphone pour réseau téléphonique commuté (RTC) avec un numéro de téléphone acquis via Azure.                                    | React |  |  |
| Composite de conversation de groupe    | Expérience de conversation légère pour Azure Communication Services utilisant des ressources de conception Fluent UI. Cette expérience se borne à fournir un client de conversation simple capable de se connecter à des threads Azure Communication Services. Il permet aux utilisateurs d’envoyer des messages et de voir ceux qu’ils ont reçus avec des indicateurs de saisie et des accusés de lecture. Il est adapté à des scénarios de conversation un-à-un ou de groupe. Prend en charge un seul thread de conversation.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Composants de base de l’infrastructure d’interface utilisateur

| Composant             | Description                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Fournisseur appelant    | Principal composant d’initialisation de l’appel. Composant nécessaire pour ensuite initialiser d’autres composants en plus de celui-ci. Gère la logique de base pour initialiser le client d’appel avec des jetons d’accès Azure Communication Services. Prend en charge la participation à un groupe. | React | N/A     | N/A |
| Contrôles média   | Permet aux utilisateurs de gérer l’appel en cours en activant ou désactivant le son, la vidéo et en terminant l’appel.                                                                                                                                                              | React | N/A     | N/A |
| Galerie multimédia   | Présente tous les participants de l’appel dans une même galerie. La galerie prend en charge les participants statiques et ceux utilisant la vidéo. Pour les participants utilisant la vidéo, la vidéo s’affiche.                                                                                                                | React | N/A     | N/A |
| Paramètres du micro | Sélectionnez le microphone à utiliser pour l’appel. Ce contrôle peut être utilisé avant et pendant un appel pour sélectionner le micro.                                                                                                                                               | React | N/A     | N/A |
| Paramètres de la caméra     | Sélectionnez la caméra à utiliser pour l’appel vidéo. Ce contrôle peut être utilisé avant et pendant un appel pour sélectionner le périphérique vidéo.                                                                                                                                             | React | N/A     | N/A |
| Paramètres de l’appareil     | Combine les paramètres du microphone et de la caméra dans un même composant                                                                                                 | React | N/A     | N/A |
| Fournisseur de conversation       | Principal composant d’initialisation de la conversation. Composant nécessaire pour ensuite initialiser d’autres composants en plus de celui-ci. Gère la logique de base pour initialiser le client de conversation avec un jeton d’accès Azure Communication Services et le thread auquel il se joindra.                                     | React | N/A     | N/A |
| Boîte d’envoi          | Composant d’entrée qui permet aux utilisateurs d’envoyer des messages au thread de conversation. L’entrée prend en charge le texte, les liens hypertexte, les emojis et les autres caractères Unicode, y compris d’autres alphabets.                                                                                                                         | React | N/A     | N/A |
| Thread de conversation           | Composant de thread qui présente à l’utilisateur les messages reçus et les messages envoyés avec les informations concernant leur expéditeur. Le thread prend en charge les indicateurs de saisie et les accusés de lecture. Vous pouvez faire défiler ces threads pour consulter l’historique de conversation.
| Liste des participants      | Affiche tous les participants de l’appel ou du thread de conversation sous forme de liste.  | React | N/A     | N/A |

## <a name="ui-framework-capabilities"></a>Fonctionnalités de l’infrastructure d’interface utilisateur

| Fonctionnalité                                                             | Composite d’appel de groupe | Composite de conversation de groupe | Composants de base |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Participer à une réunion Teams                                                  |                         |                      |           
| Participer à un événement en direct Teams                                               |                         |                      | 
| Lancer un appel VoIP à un utilisateur Teams                                       |                         |                      | 
| Participer à une conversation de réunion Teams                                           |                         |                      |            
| Participer à un appel Azure Communication Services avec un ID de groupe                | ✔                      |                      | ✔
| Lancer un appel VoIP à un ou plusieurs utilisateurs d’Azure Communication Services |                         |                      |           
| Participer à un thread de conversation Azure Communication Services                    |                         | ✔                   | ✔
| Désactiver/réactiver le son lors d’un appel                                                    | ✔                       |                      | ✔
| Activer/désactiver la vidéo lors d’un appel                                                | ✔                       |                      | ✔
| Partage d’écran                                                      | ✔                       |                      | ✔
| Galerie des participants                                                 | ✔                       |                      | ✔
| Gestion du micro                                               | ✔                       |                      | ✔
| Gestion de la caméra                                                   | ✔                       |                      | ✔
| Salle d’attente                                                          |                         |                      | ✔
| Envoyer un message de conversation                                                   |                         | ✔                   |            
| Recevoir un message de conversation                                                |                         | ✔                   | ✔
| Indicateurs de saisie                                                   |                         | ✔                   | ✔
| Accusé de lecture                                                        |                         | ✔                   | ✔
| Liste des participants                                                    |                         |                      | ✔


## <a name="customization-support"></a>Prise en charge de la personnalisation

| Type de composant            | Thèmes     | Layout                                                              | Modèles de données |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Composant composite       |     N/A    | N/A                                                                 |     N/A     |
| Composants de base            |     N/A    | La disposition des composants peut être modifiée avec des styles externes         |     N/A     |


## <a name="platform-support"></a>Plateforme prise en charge

| Kit SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| SDK IU | Chrome\*, nouveau Edge | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Notez que la dernière version de Chrome est prise en charge en plus des deux versions précédentes.

\*\*Notez que les versions 13.1+ de Safari sont prises en charge. La vidéo sortante pour Safari macOS n’est pas encore prise en charge, mais elle est prise en charge sur iOS. Le partage d’écran sortant est pris en charge uniquement sur iOS pour ordinateur de bureau.
