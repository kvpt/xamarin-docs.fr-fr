---
title: Gestion des fragments
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027319"
---
# <a name="managing-fragments"></a>Gestion des fragments

Pour aider à gérer Fragments, Android fournit la `FragmentManager` classe. Chaque activité a `Android.App.FragmentManager` un exemple qui trouvera ou changera dynamiquement ses Fragments. Chaque ensemble de ces modifications est connu sous le nom d’une `Android.App.FragmentTransation` *transaction*, et est effectuée en utilisant l’une des API contenues dans la classe , qui est géré par le `FragmentManager`. Une activité peut commencer une transaction comme celle-ci :

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Ces modifications aux fragments `FragmentTransaction` sont effectuées dans `Add()`le `Remove(),` `Replace().` cas en utilisant des `Commit()`méthodes telles que , et les modifications sont ensuite appliquées en utilisant . Les modifications apportées à une transaction ne sont pas effectuées immédiatement.
Au lieu de cela, ils sont programmés pour fonctionner sur le thread d’interface utilisateur de l’activité dès que possible.

L’exemple suivant montre comment ajouter un fragment à un conteneur existant :

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Si une transaction `Activity.OnSaveInstanceState()` est validée après est appelée, une exception sera lancée. Cela se produit parce que lorsque l’activité sauve son état, Android enregistre également l’état de tous les fragments hébergés. Si des transactions Fragment sont engagées après ce point, l’état de ces transactions sera perdu lorsque l’activité sera rétablie.

Il est possible d’enregistrer les transactions Fragment à la pile `FragmentTransaction.AddToBackStack()` [arrière](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) de l’activité en faisant un appel à . Cela permet à l’utilisateur de naviguer vers l’arrière à travers les changements de fragment lorsque le bouton **Back** est appuyé. Sans appel à cette méthode, les fragments supprimés seront détruits et ne seront pas disponibles si l’utilisateur navigue à travers l’activité.

L’exemple suivant montre `AddToBackStack` comment utiliser `FragmentTransaction` la méthode d’un fragment pour remplacer un, tout en préservant l’état du premier fragment sur la pile arrière :

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```

## <a name="communicating-with-fragments"></a>Communiquer avec des fragments

Le *FragmentManager* connaît tous les Fragments qui sont attachés à une activité et fournit deux méthodes pour aider à trouver ces fragments:

- **FindFragmentById** &ndash; Cette méthode trouvera un fragment en utilisant l’ID qui a été spécifié dans le fichier de mise en page ou l’ID du conteneur lorsque le fragment a été ajouté dans le cadre d’une transaction.

- **FindFragmentByTag** &ndash; Cette méthode est utilisée pour trouver un fragment qui a une étiquette qui a été fournie dans le fichier de mise en page ou qui a été ajouté dans une transaction.

Les deux Fragments `FragmentManager`et Activités référencent le , de sorte que les mêmes techniques sont utilisées pour communiquer d’avant en arrière entre eux. Une application peut trouver un fragment de référence en utilisant l’une de ces deux méthodes, en faisant la référence au type approprié, puis en appelant directement les méthodes sur le Fragment. L’extrait suivant donne l’exemple :

Il est également possible pour `FragmentManager` l’activité d’utiliser le pour trouver des fragments:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Communiquer avec l’activité

Il est possible pour un `Fragment.Activity` fragment d’utiliser la propriété pour référencer son hôte. En plaçant l’activité à un type plus spécifique, il est possible pour une activité d’appeler des méthodes et des propriétés sur son hôte, comme le montre l’exemple suivant :

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
