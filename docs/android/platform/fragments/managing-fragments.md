---
title: Gestion des fragments
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: f5baf8b46571c9b528fcc666a3f1f4530f18ee07
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761503"
---
# <a name="managing-fragments"></a>Gestion des fragments

Pour faciliter la gestion des fragments, Android fournit `FragmentManager` la classe. Chaque activité a une instance de `Android.App.FragmentManager` qui trouvera ou modifie dynamiquement ses fragments. Chaque ensemble de ces modifications est connu sous le nom de *transaction*et est exécuté à l’aide de l’une des API contenues dans la classe `Android.App.FragmentTransation`, qui est gérée par le. `FragmentManager` Une activité peut démarrer une transaction comme celle-ci :

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Ces modifications apportées aux fragments sont effectuées `FragmentTransaction` dans l’instance à l’aide `Add()`de méthodes `Replace().` telles que, `Remove(),` et les modifications sont `Commit()`ensuite appliquées à l’aide de. Les modifications apportées à une transaction ne sont pas effectuées immédiatement.
Au lieu de cela, ils sont planifiés pour s’exécuter sur le thread d’interface utilisateur de l’activité dès que possible.

L’exemple suivant montre comment ajouter un fragment à un conteneur existant :

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Si une transaction est validée `Activity.OnSaveInstanceState()` après l’appel de, une exception est levée. Cela se produit parce que lorsque l’activité enregistre son état, Android enregistre également l’état de tous les fragments hébergés. Si des transactions de fragment sont validées après ce point, l’état de ces transactions est perdu lorsque l’activité est restaurée.

Il est possible d’enregistrer les transactions de fragments dans la [pile Back](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) de l’activité en effectuant un appel à `FragmentTransaction.AddToBackStack()`. Cela permet à l’utilisateur de naviguer vers l’arrière à travers les modifications de fragment lorsque le bouton **précédent** est enfoncé. Sans appel à cette méthode, les fragments supprimés sont détruits et ne sont pas disponibles si l’utilisateur navigue en arrière dans l’activité.

L’exemple suivant montre comment utiliser la `AddToBackStack` méthode d’un `FragmentTransaction` pour remplacer un fragment, tout en préservant l’état du premier fragment sur la pile de retour :

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

## <a name="communicating-with-fragments"></a>Communication avec les fragments

Le *FragmentManager* connaît tous les fragments attachés à une activité et fournit deux méthodes pour aider à trouver ces fragments :

- **FindFragmentById** &ndash; Cette méthode trouvera un fragment en utilisant l’ID spécifié dans le fichier de disposition ou l’ID de conteneur lorsque le fragment a été ajouté dans le cadre d’une transaction.

- **FindFragmentByTag** &ndash; Cette méthode est utilisée pour rechercher un fragment qui a une balise fournie dans le fichier de disposition ou qui a été ajoutée dans une transaction.

Les deux fragments et les activités `FragmentManager`référencent le, donc les mêmes techniques sont utilisées pour communiquer entre eux. Une application peut trouver un fragment de référence à l’aide de l’une de ces deux méthodes, effectuer une conversion de cette référence vers le type approprié, puis appeler directement des méthodes sur le fragment. L’extrait de code suivant fournit un exemple :

Il est également possible que l’activité utilise le pour `FragmentManager` Rechercher des fragments :

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Communication avec l’activité

Il est possible pour un fragment d’utiliser la `Fragment.Activity` propriété pour référencer son hôte. En convertissant l’activité en un type plus spécifique, il est possible pour une activité d’appeler des méthodes et des propriétés sur son hôte, comme illustré dans l’exemple suivant :

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
