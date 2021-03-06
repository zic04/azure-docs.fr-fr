---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710957"
---
Stockage Azure fournit les mesures de transaction suivantes dans Azure Monitor.

| Métrique | Description |
| ------------------- | ----------------- |
| Transactions | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. <br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimensions applicables : ResponseType, GeoType, ApiName et Authentication ([Définition](#metrics-dimensions))<br/> Exemple de valeur : 1 024 |
| Entrée | Quantité de données d’entrée. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Sortie | Quantité de données de sortie. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessServerLatency | Durée moyenne utilisée pour traiter une requête réussie par Stockage Azure. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessE2ELatency | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Disponibilité | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris les requêtes qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. <br/><br/> Unité : Pourcentage <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 99,99 |