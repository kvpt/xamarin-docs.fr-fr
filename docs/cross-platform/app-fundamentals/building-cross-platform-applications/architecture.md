---
title: 'Partie 2 : Architecture'
description: Ce document décrit les modèles d’architecture utiles pour créer des applications multiplateformes. Il traite des couches d’application typiques (couche de données, couche d’accès aux données, etc.) et des modèles de logiciels mobiles courants (MVVM, MVC, etc.).
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: e1b1a98bf06bbd03b382f0b7263e6965d4efad15
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762118"
---
# <a name="part-2---architecture"></a>Partie 2 : Architecture

Un principe clé de la création d’applications multiplateformes consiste à créer une architecture qui se prête à maximiser le partage de code entre les plateformes. L’adhésion aux principes de programmation orientés objet suivants permet de créer une application bien conçue :

- **Encapsulation** : s’assurer que les classes et même les couches architecturales exposent uniquement une API minimale qui effectue leurs fonctions requises et masque les détails d’implémentation. Au niveau de la classe, cela signifie que les objets se comportent comme des « cases noires » et que la consommation de code n’a pas besoin de savoir comment effectuer leurs tâches. Au niveau architectural, cela implique l’implémentation de modèles comme la façade qui encouragent une API simplifiée qui orchestre des interactions plus complexes pour le compte du code dans des couches plus abstraites. Cela signifie que le code de l’interface utilisateur (par exemple) doit uniquement être responsable de l’affichage des écrans et de l’acceptation de l’entrée utilisateur ; et n’interagissent jamais directement avec la base de données. De même, le code d’accès aux données doit uniquement lire et écrire dans la base de données, mais ne jamais interagir directement avec des boutons ou des étiquettes.
- **Séparation des responsabilités** : Assurez-vous que chaque composant (au niveau de l’architecture et au niveau de la classe) a un objectif clair et bien défini. Chaque composant doit effectuer uniquement ses tâches définies et exposer cette fonctionnalité via une API qui est accessible aux autres classes qui doivent l’utiliser.
- **Polymorphisme** : la programmation sur une interface (ou une classe abstraite) qui prend en charge plusieurs implémentations signifie que le code de base peut être écrit et partagé entre les plateformes, tout en interagissant avec les fonctionnalités spécifiques à la plateforme.

Le résultat naturel est une application modélisée après des entités réelles ou abstraites avec des couches logiques distinctes. La séparation du code en couches rend les applications plus faciles à comprendre, à tester et à entretenir. Il est recommandé que le code de chaque couche soit physiquement séparé (dans des répertoires ou même des projets distincts pour les applications très volumineuses), ainsi que logiquement séparé (à l’aide d’espaces de noms).

 <a name="Typical_Application_Layers" />

## <a name="typical-application-layers"></a>Couches d’application standard

Tout au long de ce document et des études de cas, nous faisons référence aux six couches d’application suivantes :

- **Couche de données** : persistance des données non volatiles, susceptible d’être une base de données SQLite mais pouvant être implémentée avec des fichiers XML ou tout autre mécanisme approprié.
- **Couche d’accès aux données** : wrapper autour de la couche de données qui fournit l’accès en création, lecture, mise à jour, suppression (CRUD) aux données sans exposer les détails d’implémentation à l’appelant. Par exemple, la couche DAL peut contenir des instructions SQL pour interroger ou mettre à jour les données, mais le code de référence n’a pas besoin de le connaître.
- **Couche métier** : (parfois appelée couche de logique métier ou couche BLL) contient les définitions d’entités métier (le modèle) et la logique métier. Candidat pour le modèle de façade métier.
- **Couche d’accès** aux services – permet d’accéder aux services dans le Cloud : à partir de services Web complexes (REST, JSON, WCF) pour une récupération simple des données et des images à partir de serveurs distants. Encapsule le comportement de mise en réseau et fournit une API simple à consommer par l’application et les couches d’interface utilisateur.
- **Couche application** : code qui est généralement spécifique à la plateforme (qui n’est généralement pas partagé entre les plateformes) ou code spécifique à l’application (qui n’est généralement pas réutilisable). Un bon test pour déterminer si le code doit être placé dans la couche d’application par rapport à la couche d’interface utilisateur (a) pour déterminer si la classe a des contrôles d’affichage réels ou (b) s’il peut être partagé entre plusieurs écrans ou périphériques (par exemple, iPhone et iPad).
- **Couche de l’interface utilisateur (IU)** : couche accessible à l’utilisateur, contenant les écrans, les widgets et les contrôleurs qui les gèrent.

Une application ne doit pas nécessairement contenir toutes les couches, par exemple la couche d’accès au service n’existe pas dans une application qui n’accède pas aux ressources du réseau. Une application très simple peut fusionner la couche de données et la couche d’accès aux données, car les opérations sont extrêmement basiques.

 <a name="Common_Mobile_Software_Patterns" />

## <a name="common-mobile-software-patterns"></a>Modèles courants de logiciels mobiles

Les modèles sont un moyen établi pour capturer des solutions récurrentes à des problèmes courants. Quelques modèles clés sont utiles pour comprendre la création d’applications mobiles gérables/compréhensibles.

- **Model, View, ViewModel (MVVM)** – le modèle Model-View-ViewModel est populaire avec les infrastructures qui prennent en charge la liaison de données, telles que Xamarin. Forms. Il a été répandu par les kits de développement logiciel (SDK) compatibles XAML, tels que Windows Presentation Foundation (WPF) et Silverlight. où le ViewModel agit comme un passage entre les données (modèle) et l’interface utilisateur (vue) via la liaison de données et les commandes.
- **Modèle, vue, contrôleur (MVC)** : modèle commun et souvent mal compris, MVC est le plus souvent utilisé lors de la création d’interfaces utilisateur et fournit une séparation entre la définition réelle d’un écran d’interface utilisateur (affichage), le moteur situé derrière celui-ci qui gère l’interaction ( ) Et les données qui le remplissent (modèle). Le modèle est en fait une pièce entièrement facultative et, par conséquent, le cœur de la compréhension de ce modèle se trouve dans la vue et le contrôleur. MVC est une approche répandue pour les applications iOS.
- Le modèle **Business Facade** – alias Manager fournit un point d’entrée simplifié pour un travail complexe. Par exemple, dans une application de suivi des tâches, vous pouvez `TaskManager` avoir une classe avec des `GetAllTasks()` méthodes telles `SaveTask (task)` que, `GetTask(taskID)` ,, etc. La `TaskManager` classe fournit une façade au fonctionnement interne de l’enregistrement et de la récupération des objets de tâches.
- **Singleton** : le modèle Singleton fournit un moyen dans lequel une seule instance d’un objet particulier peut exister. Par exemple, lors de l’utilisation de SQLite dans les applications mobiles, vous ne voulez jamais qu’une seule instance de la base de données. L’utilisation du modèle Singleton est un moyen simple de s’en assurer.
- **Fournisseur** : modèle inventé par Microsoft (sans doute similaire à la stratégie ou à l’injection de dépendances de base) pour encourager la réutilisation de code dans les applications Silverlight, WPF et WinForms. Le code partagé peut être écrit par rapport à une interface ou une classe abstraite, et les implémentations concrètes spécifiques à la plateforme sont écrites et transmises lorsque le code est utilisé.
- **Async** : à ne pas confondre avec le mot clé Async, le modèle asynchrone est utilisé lorsque le travail de longue durée doit être exécuté sans l’interface utilisateur ou le traitement actuel. Dans sa forme la plus simple, le modèle asynchrone décrit simplement que les tâches longues doivent être lancées dans un autre thread (ou abstraction de threads similaire, par exemple, une tâche) tandis que le thread actuel continue à traiter et à écouter une réponse du processus en arrière-plan. , puis met à jour l’interface utilisateur lorsque les données et l’État sont retournés.

Chacun des modèles sera examiné plus en détail dans la mesure où son utilisation pratique est illustrée dans les études de cas. Wikipedia présente des descriptions plus détaillées des modèles [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [facade](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [Strategy](https://en.wikipedia.org/wiki/Strategy_pattern) et [Provider](https://en.wikipedia.org/wiki/Provider_model) (et des [modèles de conception](https://en.wikipedia.org/wiki/Design_Patterns) en général).
