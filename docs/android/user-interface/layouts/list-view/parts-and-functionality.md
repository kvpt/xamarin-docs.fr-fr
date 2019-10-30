---
title: Composants et fonctionnement de ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028863"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Fonctionnalités et composants ListView de Xamarin. Android

Un `ListView` se compose des éléments suivants :

- **Lignes** &ndash; la représentation visible des données dans la liste.

- L' **adaptateur** &ndash; une classe non visuelle qui lie la source de données à l’affichage de liste.

- Le **défilement rapide** &ndash; un handle qui permet à l’utilisateur de faire défiler la liste.

- L' **index de Section** &ndash; un élément d’interface utilisateur qui flotte sur les lignes de défilement pour indiquer où se trouvent les lignes actuelles dans la liste.

Ces captures d’écran utilisent un contrôle de `ListView` de base pour montrer comment le défilement rapide et l’index de section sont rendus :

[![des captures d’écran d’applications utilisant des lignes brutes, le défilement rapide et l’index de section](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Les éléments qui composent un `ListView` sont décrits plus en détail ci-dessous :

## <a name="rows"></a>Lignes

Chaque ligne a son propre `View`. La vue peut être l’une des vues intégrées définies dans `Android.Resources`ou une vue personnalisée. Chaque ligne peut utiliser la même disposition de vue ou elle peut être différente. Ce document contient des exemples d’utilisation de dispositions intégrées et d’autres qui expliquent comment définir des dispositions personnalisées.

## <a name="adapter"></a>Adaptateur

Le contrôle `ListView` nécessite une `Adapter` pour fournir le `View` mis en forme pour chaque ligne. Android contient des adaptateurs et des vues intégrés qui peuvent être utilisés, ou des classes personnalisées qui peuvent être créées.

## <a name="fast-scrolling"></a>Défilement rapide

Lorsqu’un `ListView` contient de nombreuses lignes de données, le défilement rapide peut être activé pour permettre à l’utilisateur d’accéder à n’importe quelle partie de la liste. La « barre de défilement » de défilement rapide peut être activée (et personnalisée au niveau de l’API 11 et versions ultérieures).

## <a name="section-index"></a>Index de section

Lorsque vous faites défiler les longues listes, l’index de section facultatif fournit à l’utilisateur des commentaires sur la partie de la liste affichée actuellement. Elle s’applique uniquement aux longues listes, en général conjointement avec le défilement rapide.

## <a name="classes-overview"></a>Vue d’ensemble des classes

Les classes principales utilisées pour afficher `ListViews` sont indiquées ici :

[![diagramme UML illustrant les relations entre ListView et les classes associées](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

L’objectif de chaque classe est décrit ci-dessous :

- **ListView** &ndash; élément d’interface utilisateur qui affiche une collection de lignes avec défilement. Sur les téléphones, elle utilise généralement la totalité de l’écran (auquel cas, la classe `ListActivity` peut être utilisée) ou elle peut faire partie d’une disposition plus grande sur les téléphones ou les appareils tablettes.

- **Affichage** &ndash; une vue dans Android peut être n’importe quel élément de l’interface utilisateur, mais dans le contexte d’une `ListView` elle requiert qu’un `View` soit fourni pour chaque ligne.

- **BaseAdapter** &ndash; classe de base pour les implémentations d’adaptateur pour lier un `ListView` à une source de données.

- **ArrayAdapter** &ndash; classe d’adaptateur intégrée qui lie un tableau de chaînes à un `ListView` pour l’affichage. Le `ArrayAdapter<T>` générique fait de même pour d’autres types.

- **CursorAdapter** &ndash; utiliser `CursorAdapter` ou `SimpleCursorAdapter` pour afficher des données en fonction d’une requête SQLite.

Ce document contient des exemples simples qui utilisent un `ArrayAdapter`, ainsi que des exemples plus complexes qui requièrent des implémentations personnalisées de `BaseAdapter` ou `CursorAdapter`.
