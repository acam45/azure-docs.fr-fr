---
title: Exemples Azure CLI pour Azure Cosmos DB | Microsoft Docs
description: Exemples de CLI Azure - Créer et gérer des bases de données, conteneurs, régions et pare-feu et comptes Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: a6c6f61f9aa3e821ec048c1516c0e433c3c64e30
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963966"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemples de CLI pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Cosmos DB. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Créer un compte, une base de données et des conteneurs Azure Cosmos DB**||
|[Créer un compte d’API SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte unique d’API Azure Cosmos DB avec base de données et conteneur, à utiliser avec l’API SQL. |
| [Créer un compte d’API MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte unique d’API DocumentDB Azure MongoDB avec base de données et collection. |
| [Créer un compte d’API Gremlin](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte unique d’API Gremlin Azure Cosmos DB avec base de données et conteneur. |
|**Mettre à l’échelle Azure Cosmos DB**||
| [Mettre à l’échelle le débit d’un conteneur](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Modifie le débit approvisionné sur un conteneur.|
|[Répliquer un compte de base de données Azure Cosmos DB dans plusieurs régions et configurer les priorités de basculement](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Réplique les données de compte globalement dans plusieurs régions avec une priorité de basculement spécifié.|
|**Sécuriser Azure Cosmos DB**||
| [Obtenir les clés de compte](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtient les clés primaire et secondaire d’écriture maître et les clés primaire et secondaire de lecture seule pour le compte.|
| [Obtenir la chaîne de connexion MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtient la chaîne de connexion pour connecter votre application MongoDB à votre compte Azure Cosmos DB.|
|[Régénération des clés de compte](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Régénère la clé de maître ou en lecture seule pour le compte.|
|[Créer un pare-feu](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crée une stratégie de contrôle d’accès des IP entrantes pour limiter l’accès au compte à un ensemble de machines et/ou services cloud approuvés.|
|**Haute disponibilité et récupération d'urgence, sauvegarde et restauration**||
|[Configurer la stratégie de basculement](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Définit la priorité de basculement de chaque région dans laquelle le compte est répliqué.|
|**Connecter Azure Cosmos DB aux ressources**||
|[Connecter une application web à Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Créez et connectez une base de données Azure Cosmos DB et une application web Azure.|
|||
