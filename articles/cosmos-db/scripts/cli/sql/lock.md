---
title: Créer un verrou de ressource pour une base de données et un conteneur d’API (SQL) Core Azure Cosmos DB
description: Créer un verrou de ressource pour une base de données et un conteneur d’API (SQL) Core Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: b14bc8f77c6f26fba36880762d12723888d845e3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565991"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Créer un verrou de ressource pour une base de données et un conteneur d’API (SQL) Core Azure Cosmos DB à partir d’Azure CLI
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.9.1 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent via un SDK Cosmos DB, les outils qui se connectent via des clés de compte ou le portail Azure, à moins que le compte Cosmos DB soit d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Crée un verrou. |
| [az lock list](/cli/azure/lock#az-lock-list) | Liste les informations sur les verrous. |
| [az lock show](/cli/azure/lock#az-lock-show) | Affiche les propriétés d’un verrou. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Supprime un verrou. |

## <a name="next-steps"></a>Étapes suivantes

- [Verrouiller les ressources pour empêcher les modifications inattendues](../../../../azure-resource-manager/management/lock-resources.md)

- [Documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

- [Dépôt GitHub de l’interface CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
