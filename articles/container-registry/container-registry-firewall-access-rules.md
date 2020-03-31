---
title: Règles d’accès du pare-feu
description: Configurez des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu, en autorisant l’accès à des noms de domaine d’API REST et de point de terminaison de stockage, ou à des plages d’adresses IP spécifiques au service (« mise en liste verte »).
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168016"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu

Cet article explique comment configurer des règles sur votre pare-feu pour autoriser l’accès à un registre de conteneurs Azure. Par exemple, un appareil Azure IoT Edge situé derrière un pare-feu ou un serveur proxy peut devoir accéder à un registre de conteneurs pour extraire une image de conteneur. Un serveur verrouillé sur un réseau local peut aussi devoir y accéder pour envoyer (push) une image.

Si, au lieu de cela, vous souhaitez configurer des règles d’accès réseau de trafic entrant sur un registre de conteneurs, uniquement au sein d’un réseau virtuel Azure ou à partir d’une plage d’adresses IP publiques, consultez [Restreindre l’accès à un registre de conteneurs Azure à partir d’un réseau virtuel](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>À propos des points de terminaison de registre

Pour extraire ou envoyer (push) des images ou d’autres artefacts à un registre de conteneurs Azure, un client (démon Docker, par exemple) doit interagir sur HTTPS avec deux points de terminaison distincts.

* **Point de terminaison d'API REST de registre** - Les opérations d'authentification et de gestion de registre sont gérées via le point de terminaison de l’API REST publique du registre. Ce point de terminaison correspond au nom du serveur de connexion du registre ou à une plage d’adresses IP associée. 

* **Point de terminaison de stockage** – Azure [alloue du stockage d’objets blob](container-registry-storage.md) aux comptes de Stockage Azure pour le compte de chaque registre, afin de gérer les données des images conteneur et d’autres artefacts. Lorsqu’un client accède à des couches d’image dans un registre de conteneurs Azure, il fait des requêtes à l’aide d’un point de terminaison de compte de stockage fourni par le registre.

Si votre registre est [géorépliqué](container-registry-geo-replication.md), un client peut devoir interagir avec les points de terminaison REST et les points de terminaison de stockage dans une région spécifique ou dans plusieurs régions répliquées.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Autoriser l’accès aux noms de domaine de stockage et de REST

* **Point de terminaison REST** – Autorisez l’accès au nom complet du serveur de connexion au registre, par exemple `myregistry.azurecr.io`
* **Point de terminaison de (données de) stockage** – Autorisez l’accès à tous les comptes de stockage d’objets blob Azure à l’aide du caractère générique `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Autoriser l’accès en fonction de la plage d’adresses IP

Si votre organisation dispose de stratégies pour autoriser l’accès uniquement à des adresses IP ou à des plages d’adresses spécifiques, téléchargez [Plages d’adresses IP et étiquettes des services Azure – Cloud public](https://www.microsoft.com/download/details.aspx?id=56519).

Afin de rechercher les plages d’adresses IP du point de terminaison ACR REST pour lesquelles vous devez autoriser l’accès, recherchez **AzureContainerRegistry** dans le fichier JSON.

> [!IMPORTANT]
> Les plages d’adresses IP des services Azure peuvent changer et des mises à jour sont publiées chaque semaine. Téléchargez régulièrement le fichier JSON et effectuez les mises à jour nécessaires dans vos règles d’accès. Si votre scénario implique la configuration de règles de groupe de sécurité réseau dans un réseau virtuel Azure pour accéder à Azure Container Registry, utilisez plutôt l’**étiquette de service** [AzureContainerRegistry](#allow-access-by-service-tag).
>

### <a name="rest-ip-addresses-for-all-regions"></a>Adresses IP REST pour toutes les régions

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Adresses IP REST pour une région spécifique

Recherchez la région spécifique, par exemple **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Adresses IP de stockage pour toutes les régions

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Adresses IP de stockage pour des régions spécifiques

Recherchez la région spécifique, par exemple **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Autoriser l’accès par balise de service

Dans un réseau virtuel Azure, utilisez les règles de sécurité réseau pour filtrer le trafic d’une ressource, telle qu’une machine virtuelle, vers un registre de conteneurs. Pour simplifier la création des règles de réseau Azure, utilisez l’**étiquette de service** [AzureContainerRegistry](../virtual-network/security-overview.md#service-tags). Une balise de service représente un groupe de préfixes d’adresses IP permettant d'accéder à un service Azure globalement ou par région Azure. La balise est automatiquement mise à jour lorsque les adresses changent. 

Par exemple, créez une règle de groupe de sécurité réseau sortante avec la destination **AzureContainerRegistry** pour autoriser le trafic vers un registre de conteneurs Azure. Pour autoriser l’accès à la balise de service uniquement dans une région spécifique, indiquez la région au format suivant : **AzureContainerRegistry**.[*nom de la région*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Configurer les règles de pare-feu client pour MCR

Si vous devez accéder à Microsoft Container Registry (MCR) derrière un pare-feu, consultez les instructions de configuration des [règles de pare-feu du client MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR est le registre principal pour toutes les images Docker publiées par Microsoft, comme les images de Windows Server.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [meilleures pratiques Azure pour la sécurité réseau](../security/fundamentals/network-best-practices.md)

* En savoir plus sur les [groupes de sécurité](/azure/virtual-network/security-overview) dans un réseau virtuel Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

